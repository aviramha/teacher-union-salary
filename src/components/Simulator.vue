<template>
  <v-container>
    <v-row justify="center" class="text-center" align="center">
      <v-col cols="12" align-self="center">
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
          <v-btn :disabled="!valid" color="success" class="mr-4"> חשב </v-btn>
        </v-form>
      </v-col>
      <v-col>
        <v-data-table
          disable-pagination
          disable-sort
          :hide-default-footer="true"
          :headers="headers"
          :items="compensations"
        >
        </v-data-table>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
const thisJewishYearBases = {
  "תואר ראשון": 5806,
  "תואר שני": 6095.9325,
};

const nextJewishYearBases = {
  "תואר ראשון": 6300,
  "תואר שני": 6615,
};

// not sure what it means?
const level_7_5 = 7.5 / 100;
const seniority_2 = 2 / 100;
const seniority_1 = 1 / 100;
const seniority2Years = 7;
const seniority1Year = 36;

const levelShiklit = [
  1114.46, 983.98, 826.07, 741.53, 563.6, 563.6, 563.6, 563.6, 563.6,
];

const firstDegreeAddition = [
  1585.54, 1335.54, 1085.54, 835.54, 585.54, 335.54, 185.54, 35.54, 0,
];

const secondDegreeAddition = [
  1664.87, 1402.37, 1139.87, 877.37, 614.87, 352.37, 194.87, 37.37, 0,
];

// תשפ״ב
const roleCompensationLastYear = {
  "ריכוז שכבה (עד 4 כיתות)": 6 / 100,
  "ריכוז שכבה (מעל 4 כיתות)": 6 / 100,
  "ריכוז חברתי - חט״ב": 10 / 100,
  "ריכוז חברתי - בי״ס יסודי": 6 / 100,
  "ריכוז פדגוגי": 6 / 100,
  "סגנות שניה ומעלה": 13 / 100,
  "ניהול חטיבה צעירה": 13 / 100,
  "ריכוז אחר (6%)": 6 / 100,
};

// as far as I understand they take either finite sum or percentage based on max
// תשפ״ד
const roleCompensationThisYearPercentage = {
  "ריכוז שכבה (עד 4 כיתות)": 7 / 100,
  "ריכוז שכבה (מעל 4 כיתות)": 6 / 100,
  "ריכוז חברתי - חט״ב": 10 / 100,
  "ריכוז חברתי - בי״ס יסודי": 8 / 100,
  "ריכוז פדגוגי": 8 / 100,
  "סגנות שניה ומעלה": 20 / 100,
  "ניהול חטיבה צעירה": 20 / 100,
  "ריכוז אחר (6%)": 6 / 100,
};

// תשפ״ד
const roleCompensationNextSum = {
  "ריכוז שכבה (עד 4 כיתות)": 1100,
  "ריכוז שכבה (מעל 4 כיתות)": 1100,
  "ריכוז חברתי - חט״ב": 0,
  "ריכוז חברתי - בי״ס יסודי": 0,
  "ריכוז פדגוגי": 0,
  "סגנות שניה ומעלה": 1100,
  "ניהול חטיבה צעירה": 1100,
  "ריכוז אחר (6%)": 0,
};

// כיתה א׳
const hinuchA = 11.5 / 100;

// כיתה ב׳-ט׳
const hinuchRest = 10 / 100;

// same for both options (א-ט׳)
const finiteCompHinuch = 1000;

function calcRoleCompensation(
  year,
  role,
  percentage,
  twentytwoAddition,
  mixedCompensation
) {
  if (role == "ללא") {
    return 0;
  }
  let salary = mixedCompensation + twentytwoAddition;
  if (year == "תשפ״ב" || year == "תשפ״ג") {
    return salary * roleCompensationLastYear[role] * percentage;
  } else if (year == "תשפ״ד") {
    return Math.max(
      salary * roleCompensationThisYearPercentage[role] * percentage,
      roleCompensationNextSum[role]
    );
  }
}

function calculateHinuch(
  percentage,
  twentytwoAddition,
  mixedCompensation,
  year,
  hinuch
) {
  let salary = mixedCompensation + twentytwoAddition;
  var comp = salary * hinuch;
  if (year == "תשפ״ד") {
    comp = Math.max(comp, finiteCompHinuch);
  }
  return comp * percentage;
}

function calculateSalary(
  degree,
  hinuchComp,
  roles,
  percentage,
  seniority,
  level,
  jewishYear
) {
  var base;
  var addition;
  if (jewishYear == "תשפ״ב") {
    base = thisJewishYearBases[degree];
    addition = 0;
  } else {
    base = nextJewishYearBases[degree];
    if (degree == "תואר ראשון") {
      addition = firstDegreeAddition[level - 1];
    } else {
      addition = secondDegreeAddition[level - 1];
    }
  }
  if (jewishYear == "תשפ״ג") {
    addition /= 2;
  }
  addition *= percentage;

  let mixedCompensation = Math.round(
    base *
      (1 + level_7_5) ** (level - 1) *
      (1 + seniority_2) ** Math.min(seniority2Years - 1, seniority - 1) *
      (1 + seniority_1) **
        Math.min(
          seniority1Year - seniority2Years,
          Math.max(0, seniority - seniority2Years)
        ) *
      percentage
  );

  var roleCompensation1 = calcRoleCompensation(
    jewishYear,
    roles[0] || "ללא",
    percentage,
    addition,
    mixedCompensation
  );
  var roleCompensation2 = calcRoleCompensation(
    jewishYear,
    roles[1] || "ללא",
    percentage,
    addition,
    mixedCompensation
  );

  var hinuchCompensation = 0;
  if (hinuchComp != "ללא") {
    let hinuchVariable = hinuchComp == "כיתה א׳" ? hinuchA : hinuchRest;
    hinuchCompensation = calculateHinuch(
      percentage,
      addition,
      mixedCompensation,
      jewishYear,
      hinuchVariable
    );
  }
  let data = {
    mixedCompensation: mixedCompensation,
    shiklitAddition: Math.round(levelShiklit[level - 1] * percentage),
    twentytwoAddition: Math.round(addition),
    phoneReimbursement: Math.round(percentage * 48.6),
    hinuchCompensation: Math.round(hinuchCompensation),
    roleCompensation1: Math.round(roleCompensation1),
    roleCompensation2: Math.round(roleCompensation2),
    specialEducationCompensation: 0,
    kindergardenCompensation: 0,
  };
  const values = Object.values(data);

  const sum = values.reduce((accumulator, value) => {
    return accumulator + value;
  }, 0);
  data["totalCompensation"] = sum;
  data["jewishYear"] = jewishYear;

  return data;
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
        { text: "שנה", value: "jewishYear" },
        { text: "שכר משולב", value: "mixedCompensation" },
        { text: "תוספת שקלית 2016", value: "shiklitAddition" },
        { text: "תוספת 2022", value: "twentytwoAddition" },
        { text: "החזר טלפון", value: "phoneReimbursement" },
        { text: "גמול חינוך", value: "hinuchCompensation" },
        { text: "גמול תפקיד " + role1, value: "roleCompensation1" },
        { text: "גמול תפקיד " + role2, value: "roleCompensation2" },
        { text: "גמול חנ״מ", value: "specialEducationCompensation" },
        { text: "גמול גננות", value: "kindergardenCompensation" },
        { text: "סה״כ ברוטו", value: "totalCompensation" },
      ];
    },
    compensations() {
      let percentage = this.percentage / 100;
      return [
        calculateSalary(
          this.degree,
          this.hinuchComp,
          this.chosenRoles,
          percentage,
          this.seniority,
          this.level,
          "תשפ״ב"
        ),
        calculateSalary(
          this.degree,
          this.hinuchComp,
          this.chosenRoles,
          percentage,
          this.seniority,
          this.level,
          "תשפ״ג"
        ),
        calculateSalary(
          this.degree,
          this.hinuchComp,
          this.chosenRoles,
          percentage,
          this.seniority,
          this.level,
          "תשפ״ד"
        ),
      ];
    },
  },
};
</script>
