<template>
  <v-app>
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
                  >מורים מובילים</a
                >
              </v-card-subtitle>
              <v-card-text
                >זהו סימולטור שכר לשנים תשפ״ב, תשפ״ג, תשפ״ד למורים ומורות החברות
                בהסתדרות המורים</v-card-text
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
            >מורים מובילים</a
          >
          האתר ניתן ללא אחריות מצד התורם/מפתח, הארגון או השולח וניתן לציבור
          בחינם.
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
