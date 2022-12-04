<template>
  <v-app style="background: #ECEAE1;">
    <v-main>
      <v-container>
        <v-row justify="center" class="text-center" align="center">
          <v-col cols="12" align-self="center">
            <v-card style="background: #ECEAE1;">
              <v-card-title class="justify-center"
                >סימולטור שכר</v-card-title
              >
              <v-card-subtitle 
                >מידע זה ניתן בחינם, ללא ערבות ואחריות על ידי
                <br/>
                <a href="https://www.morimovilim.org/"><img src="/logo.webp" />
                  <br/>לאתר מורים מובילים שינוי לחץ כאן
                </a>
                <p>
                  <a
                    class="app-link"
                    href="https://pay.sumit.co.il/2fwesp/2fwfte/"
                  >
                    לתרומות לארגון מורים מובילים שינוי לחצו כאן
                  </a>
                </p>
              </v-card-subtitle>
              <v-card-text
                >זהו סימולטור שכר לשנים תשפ״ב, תשפ״ג, תשפ״ד לעובדי/ות הוראה
                ברפורמת אופק חדש</v-card-text
              >
            </v-card>
          </v-col>
        </v-row>
      </v-container>
      <Simulator />
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
import Simulator from "./components/Simulator";

export default {
  name: "App",

  components: {
    Simulator,
  },

  data: () => ({
    //
  }),
};
</script>
