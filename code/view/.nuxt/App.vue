<template>
  <div id="__nuxt">
    <nuxt-loading ref="loading"></nuxt-loading>
    <component v-if="layout" :is="layout"></component>
  </div>
</template>

<script>
import NuxtLoading from './components/nuxt-loading.vue'

import '~/static/css/vuetify.min.css'


let layouts = {

  "_default": () => import('/home/docker/code/app/layouts/default.vue'  /* webpackChunkName: "layouts/default" */)

}

export default {
  head: {"title":"starter","meta":[{"charset":"utf-8"},{"name":"viewport","content":"width=device-width, initial-scale=1"},{"hid":"description","content":"djuxt"}],"link":[{"rel":"icon","type":"image/x-icon","href":"favicon.ico"},{"rel":"stylesheet","href":"https://fonts.googleapis.com/css?family=Roboto:300,400,500,700|Material+Icons","type":"text/css"}],"style":[],"script":[]},
  data: () => ({
    layout: null,
    layoutName: ''
  }),
  
  mounted () {
    this.$loading = this.$refs.loading
    this.$nuxt.$loading = this.$loading
  },
  
  methods: {
    setLayout (layout) {
      if (!layout || !layouts['_' + layout]) layout = 'default'
      this.layoutName = layout
      let _layout = '_' + layout
      this.layout = layouts[_layout]
      return this.layout
    },
    loadLayout (layout) {
      if (!layout || !layouts['_' + layout]) layout = 'default'
      let _layout = '_' + layout
      if (typeof layouts[_layout] !== 'function') {
        return Promise.resolve(layouts[_layout])
      }
      return layouts[_layout]()
      .then((Component) => {
        layouts[_layout] = Component
        return layouts[_layout]
      })
      .catch((e) => {
        if (this.$nuxt) {
          return this.$nuxt.error({ statusCode: 500, message: e.message })
        }
        console.error(e)
      })
    }
  },
  components: {
    NuxtLoading
  }
}
</script>

