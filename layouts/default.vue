<template>
  <v-app dark>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer dark padless>
      <p>
        אתר זה פותח כתרומה עבור ארגון
        <a class="app-link" href="https://www.morimovilim.org/"
          >מורים מובילים שינוי</a
        >
        האתר ניתן ללא אחריות מצד התורם/מפתח, הארגון או השולח וניתן לציבור בחינם.
      </p>
      <v-divider></v-divider>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data () {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: 'mdi-apps',
          title: 'Welcome',
          to: '/'
        },
        {
          icon: 'mdi-chart-bubble',
          title: 'Inspire',
          to: '/inspire'
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js'
    }
  }
}
</script>
