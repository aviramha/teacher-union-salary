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
            counter="2"
          ></v-select>
          <v-btn
            :disabled="!valid"
            color="success"
            class="mr-4"
          >
            חשב
          </v-btn>
        </v-form>
      </v-col>
      <v-col>
        <v-data-table disable-pagination :headers="headers" :items="compensations">
        </v-data-table>
        
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

function calculateSalary(degree, hinuchComp, roles, percentage, seniority, level, jewishYear) {
  seniority;
  level;

  let data = {
    "mixedCompensation": 0,
    "shiklitAddition": 0,
    "twentytwoAddition": 0,
    "phoneReimbursement": percentage * 49,
    "hinuchCompensation": 0,
    "roleCompensation1": 0,
    "roleCompensation2": 0,
    "specialEducationCompensation": 0,
    "kindergardenCompensation": 0,
  }
  const values = Object.values(data);

  const sum = values.reduce((accumulator, value) => {
    return accumulator + value;
  }, 0);
  data["totalCompensation"] = sum;
  data["jewishYear"] = jewishYear

  return data
}


export default {
  name: "Simulator",

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
    headers() {
      let role1 = this.chosenRoles[0] || "ללא";
      let role2 = this.chosenRoles[1] || "ללא";
      return [
        {"text": "שנה", "value": "jewishYear"},
        {"text": "שכר משולב", "value": "mixedCompensation"},
        {"text": "תוספת שקלית 2016", "value": "shiklitAddition"},
        {"text": "תוספת 2022", "value": "twentytwoAddition"},
        {"text": "החזר טלפון", "value": "phoneReimbursement"},
        {"text": "גמול חינוך", "value": "hinuchCompensation"},
        {"text": "גמול תפקיד " + role1, "value": "roleCompensation1"},
        {"text": "גמול תפקיד " + role2, "value": "roleCompensation2"},
        {"text": "גמול חנ״מ", "value": "specialEducationCompensation"},
        {"text": "גמול גננות", "value": "kindergardenCompensation"},
        {"text": "סה״כ ברוטו", "value": "totalCompensation"},
      ]
    },
    compensations() {
      let percentage = this.percentage / 100
      return [
        calculateSalary(this.degree, this.hinuchComp, this.chosenRoles, percentage, this.seniority, this.level, "תשפ״ב"),
        calculateSalary(this.degree, this.hinuchComp, this.chosenRoles, percentage, this.seniority, this.level, "תשפ״ג"),
        calculateSalary(this.degree, this.hinuchComp, this.chosenRoles, percentage, this.seniority, this.level, "תשפ״ד")
      ]
    }
  },
};
</script>
