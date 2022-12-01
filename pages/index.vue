<template>
    <v-main>
      <v-container>
        <v-row justify="center" class="text-center" align="center">
          <v-col cols="12" align-self="center">
            <v-card>
              <v-card-title justify="center" class="text-center" align="center"
                >סימולטור שכר</v-card-title
              >
              <v-card-subtitle
                >מידע זה ניתן בחינם, ללא ערבות ואחריות על ידי ארגון
                <a class="app-link" href="https://www.morimovilim.org/"
                  >מורים מובילים שינוי</a
                >
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
</template>

<script>
import Simulator from "../components/Simulator";

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
