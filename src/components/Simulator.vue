<template>
  <v-container>
    <v-row justify="center" class="text-center" align="center">
      <v-col cols="4" align-self="center">
        <v-form ref="form" v-model="valid" lazy-validation>
          <v-text-field
            v-model="percentage"
            type="number"
            min="1"
            max="100"
            label="אחוז משרה"
            required
          ></v-text-field>
          <v-text-field
            v-model="seniority"
            type="number"
            min="1"
            max="36"
            label="וותק אופק חדש"
            required
          ></v-text-field>
          <v-text-field
            v-model="level"
            type="number"
            min="1"
            max="36"
            label="דרגה אופק חדש"
            required
          ></v-text-field>

          <v-select
            v-model="degree"
            :items="degrees"
            :rules="[(v) => !!v || 'חובה לציין']"
            label="תואר"
            required
          ></v-select>
          <v-select
            v-model="hinuchComp"
            :items="hinuchComps"
            :rules="[(v) => !!v || 'חובה לציין']"
            label="גמול חינוך"
            required
          ></v-select>
          <v-select
            v-model="chosenRoles"
            :items="roles"
            attach
            chips
            label="תפקידים"
            multiple
          ></v-select>
          <v-btn
            :disabled="!valid"
            color="success"
            class="mr-4"
            @click="validate"
          >
            חשב
          </v-btn>
        </v-form>
      </v-col>
      <v-col>
        <v-simple-table>
          <template v-slot:default>
            <thead>
              <tr>
                <th class="text-left">שכר משולב</th>
                <th class="text-left">תוספת שקלית 2016</th>
                <th class="text-left">תוספת 2022</th>
                <th class="text-left">החזר טלפון</th>
                <th class="text-left">גמול חינוך</th>
                <th class="text-left">גמול תפקיד {{ chosenRoles[0] }}</th>
                <th class="text-left">גמול תפקיד {{ chosenRoles[1] }}</th>
                <th class="text-left">גמול חנ״מ</th>
                <th class="text-left">גמול גננות</th>
                <th class="text-left">סה״כ ברוטו</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in desserts" :key="item.name">
                <td>{{ mixedComp }}</td>
                <td>{{ shiklitAddition }}</td>
                <td>{{ twentytwoAddition }}</td>
                <td>{{ phoneReimbursement }}</td>
                <td>{{ hinuchCompensation }}</td>
                <td>{{ firstRoleCompensation }}</td>
                <td>{{ secondRoleCompensation }}</td>
                <td>{{ specialEducationCompensation }}</td>
                <td>{{ kindergardenCompensation }}</td>
                <td>{{ total }}</td>
              </tr>
            </tbody>
          </template>
        </v-simple-table>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
// const this_jewish_year_bases = {
//   "תואר ראשון": 5806,
//   "תואר שני": 6095.9325
// }

// const next_jewish_year_bases = {
//   "תואר ראשון": 6300,
//   "תואר שני": 6615
// }

export default {
  name: "HelloWorld",

  data: () => ({
    valid: true,
    degrees: ["תואר ראשון", "תואר שני"],
    degree: "תואר ראשון",
    hinuchComps: ["ללא", "כיתה א׳", "כיתה ב׳-ט׳"],
    hinuchComp: "ללא",
    roles: [
      "ריכוז שכבה (עד 4 כיתות)",
      "ריכוז שכבה (מעל 4 כיתות)",
      "ריכוז חברתי - חט״ב",
      "ריכוז חברתי - בי״ס יסודי",
      "ריכוז פדגוגי",
      "סגנות שניה ומעלה",
      "ניהול חטיבה צעירה",
      "ריכוז אחר (6%)",
    ],
    chosenRoles: [],
    percentage: 100,
    seniority: 1,
    level: 1,
  }),
  computed: {
    mixedComp() {
      return 0;
    },
    shiklitAddition() {
      return 0;
    },
    twentytwoAddition() {
      return 0;
    },
    phoneReimbursement() {
      return 0;
    },
    hinuchCompensation() {
      return 0;
    },
    firstRoleCompensation() {
      return 0;
    },
    secondRoleCompensation() {
      return 0;
    },
    specialEducationCompensation() {
      return 0;
    },
    kindergardenCompensation() {
      return 0;
    },
    total() {
      return 0;
    },
  },
};
</script>
