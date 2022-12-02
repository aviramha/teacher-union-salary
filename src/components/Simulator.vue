<template>
  <v-container>
    <v-row justify="center" class="text-center" align="center">
      <v-col cols="auto" align-self="center">
        <v-form ref="form" v-model="valid" lazy-validation>
          <v-text-field
            v-model="percentage"
            type="number"
            min="1"
            max="150"
            label="אחוז משרה"
            required
            :rules="percentageRules"
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
            max="9"
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
            :rules="roleRules"
          ></v-select>
          <v-switch v-model="isSpecialEducation" label="חינוך מיוחד">
          </v-switch>
          <v-container v-if="isSpecialEducation">
            <v-text-field
              v-model="schoolSpecialEdPercentage"
              type="number"
              min="0"
              max="100"
              label="% משרה חנ״מ בבי״ס"
              required
              :rules="specialEdPercentageRules"
            ></v-text-field>
            <v-text-field
              v-model="schoolExtraSpecialEdPercentage"
              type="number"
              min="0"
              max="100"
              label="% משרה חנ״מ בבי״ס אוטיזם / הפרעות נפשיות קשות"
              required
              :rules="specialEdPercentageRules"
            ></v-text-field>
            <v-text-field
              v-model="matyaSpecialEdPercentage"
              type="number"
              min="0"
              max="100"
              label="% משרה חנ״מ במתי״א"
              required
              :rules="specialEdPercentageRules"
            ></v-text-field>
            <v-text-field
              v-model="matyaExtraSpecialEdPercentage"
              type="number"
              min="0"
              max="100"
              label="% משרה חנ״מ במתי״א אוטיזם / הפרעות נפשיות קשות"
              required
              :rules="specialEdPercentageRules"
            ></v-text-field>
          </v-container>
          <v-switch v-model="isGanenet" label="גננת"> </v-switch>
          <v-container v-if="isGanenet">
            <v-select
              v-model="kindergardenRole"
              :items="kindergardenRoles"
              label="תפקיד"
              required
            ></v-select>
            <v-text-field
              v-if="kindergardenRole == 'ניהול גן (ותיקה באופק)'"
              type="number"
              min="0"
              max="10"
              v-model="kindergardenSeniority"
              label="וותק בניהול"
              required
            ></v-text-field>
          </v-container>
          <v-btn :disabled="!valid" color="success" class="mr-4"> חשב </v-btn>
          <v-btn @click="reset" :disabled="!valid" color="warning" class="mr-4">
            אפס
          </v-btn>
        </v-form>
      </v-col>
    </v-row>
    <v-container fluid>
      <v-data-iterator :items="compensations" hide-default-footer>
        <v-row justify="center" class="text-center" align="center">
          <v-col
            v-for="(item, index) in compensations"
            :key="index"
            md="4"
            align-self="center"
            sm="auto"
          >
            <v-card>
              <v-card-title class="subheading font-weight-bold">
                {{ item.jewishYear }}
              </v-card-title>

              <v-divider></v-divider>

              <v-list dense>
                <template v-for="(header, index) in headers">
                  <v-list-item :key="index">
                    <v-list-item-content>{{ header.text }}</v-list-item-content>
                    <v-list-item-content class="align-end">
                      {{ item[header.value] }}
                    </v-list-item-content>
                  </v-list-item>
                </template>
              </v-list>
            </v-card>
          </v-col>
        </v-row>
      </v-data-iterator>
    </v-container>
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
    return salary * roleCompensationLastYear[role];
  } else if (year == "תשפ״ד") {
    return Math.max(
      salary * roleCompensationThisYearPercentage[role],
      roleCompensationNextSum[role]
    );
  }
}

function calculateHinuch(
  twentytwoAddition,
  mixedCompensationRaw,
  year,
  hinuch
) {
  let salary = mixedCompensationRaw + twentytwoAddition;
  var comp = salary * hinuch;
  if (year == "תשפ״ד") {
    comp = Math.max(comp, finiteCompHinuch);
  }
  return comp;
}

// All are percentages
function calculateSpecialEd(
  school,
  extraSchool,
  matya,
  extraMatya,
  twentytwoAddition,
  mixedCompensation,
  year
) {
  let salary = mixedCompensation + twentytwoAddition;
  const schoolBonusThisYear = 14 / 100;
  // תשפ״ד
  const schoolBonusNextYear = 15 / 100;

  const extraSchoolBonusThisYear = 14 / 100;
  // תשפ״ד
  const extraSchoolBonusNextYear = 17 / 100;

  const matyaBonusThisYear = 9 / 100;
  // תשפ״ד
  const matyaBonusNextYear = 15 / 100;

  const extraMatyaBonusThisYear = 9 / 100;
  // תשפ״ד
  const extraMatyaBonusNextYear = 17 / 100;

  if (year != "תשפ״ד") {
    return (
      salary * schoolBonusThisYear * school +
      salary * extraSchoolBonusThisYear * extraSchool +
      salary * matyaBonusThisYear * matya +
      salary * extraMatyaBonusThisYear * extraMatya
    );
  } else {
    return (
      salary * schoolBonusNextYear * school +
      salary * extraSchoolBonusNextYear * extraSchool +
      salary * matyaBonusNextYear * matya +
      salary * extraMatyaBonusNextYear * extraMatya
    );
  }
}

const kinderLevelComp = [0.17, 0.17, 0.2, 0.2, 0.21, 0.21, 0.21, 0.21, 0.21];

const kinderSeniorityComp = [
  0.17, 0.17, 0.17, 0.17, 0.17, 0.17, 0.2, 0.2, 0.2, 0.2, 0.21,
];

function calculateKindergarden(
  kindergardenRole,
  kindergardenSeniority,
  level,
  percentage,
  base,
  year,
  shiklitAddition
) {
  let calcBase = base + shiklitAddition;
  if (kindergardenRole == "ניהול אשכול") {
    return 0.25 * calcBase;
  } else if (kindergardenRole == "גננת משלימה זכאית") {
    if (year == "תשפ״ד") {
      return 0.1 * calcBase * percentage;
    }
    return 0;
  } else if (kindergardenRole == "ניהול גן (חדשה באופק)") {
    let optA =
      kinderLevelComp[Math.min(level - 1, kinderLevelComp.length - 1)] *
      calcBase;
    if (year == "תשפ״ד") {
      return Math.max(optA, 1500);
    }
    return optA;
  } else if (kindergardenRole == "ניהול גן (ותיקה באופק)") {
    let optA =
      kinderSeniorityComp[
        Math.min(kindergardenSeniority - 1, kinderSeniorityComp.length - 1)
      ] * calcBase;
    if (year == "תשפ״ד") {
      return Math.max(optA, 1500);
    }
    return optA;
  }
}

function calculateSalary(
  degree,
  hinuchComp,
  roles,
  percentage,
  seniority,
  level,
  schoolSpecialEdPercentage,
  schoolExtraSpecialEdPercentage,
  matyaSpecialEdPercentage,
  matyaExtraSpecialEdPercentage,
  isSpecialEducation,
  jewishYear,
  isGanenet,
  kindergardenRole,
  kindergardenSeniority
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

  let mixedCompensationRaw =
    base *
    (1 + level_7_5) ** (level - 1) *
    (1 + seniority_2) ** Math.min(seniority2Years - 1, seniority - 1) *
    (1 + seniority_1) **
      Math.min(
        seniority1Year - seniority2Years,
        Math.max(0, seniority - seniority2Years)
      );

  let mixedCompensation = mixedCompensationRaw * percentage;
  var roleCompensation1 = calcRoleCompensation(
    jewishYear,
    roles[0] || "ללא",
    percentage,
    addition,
    mixedCompensationRaw
  );
  var roleCompensation2 = calcRoleCompensation(
    jewishYear,
    roles[1] || "ללא",
    percentage,
    addition,
    mixedCompensationRaw
  );

  var hinuchCompensation = 0;
  if (hinuchComp != "ללא") {
    let hinuchVariable = hinuchComp == "כיתה א׳" ? hinuchA : hinuchRest;
    hinuchCompensation = calculateHinuch(
      percentage,
      addition,
      mixedCompensationRaw,
      jewishYear,
      hinuchVariable
    );
  }

  var specialEdComp = 0;
  if (isSpecialEducation) {
    specialEdComp = calculateSpecialEd(
      schoolSpecialEdPercentage / 100,
      schoolExtraSpecialEdPercentage / 100,
      matyaSpecialEdPercentage / 100,
      matyaExtraSpecialEdPercentage / 100,
      addition,
      mixedCompensation,
      jewishYear
    );
  }
  var kindergardenCompensation = 0;
  let shiklitAddition = Math.round(levelShiklit[level - 1] * percentage);
  if (isGanenet) {
    kindergardenCompensation = calculateKindergarden(
      kindergardenRole,
      kindergardenSeniority,
      level,
      percentage,
      mixedCompensationRaw,
      jewishYear,
      shiklitAddition
    );
  }
  let data = {
    mixedCompensation: Math.round(mixedCompensation),
    shiklitAddition: Math.round(shiklitAddition),
    twentytwoAddition: Math.round(addition),
    phoneReimbursement: Math.round(percentage * 48.6),
    hinuchCompensation: Math.round(hinuchCompensation),
    roleCompensation1: Math.round(roleCompensation1),
    roleCompensation2: Math.round(roleCompensation2),
    specialEducationCompensation: Math.round(specialEdComp),
    kindergardenCompensation: Math.round(kindergardenCompensation),
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
  methods: {
    reset() {
      this.degree = "תואר ראשון";
      this.level = 1;
      this.seniority = 1;
      this.percentage = 100;
      this.hinuchComp = "ללא";
      this.chosenRoles = [];
      this.isSpecialEducation = false;
      this.schoolSpecialEdPercentage = 0;
      this.schoolExtraSpecialEdPercentage = 0;
      this.matyaSpecialEdPercentage = 0;
      this.matyaExtraSpecialEdPercentage = 0;
      this.jewishYear = "תשפ״ב";
      this.kindergardenRole = null;
      this.isGanenet = false;
      this.kindergardenSeniority = 0;
    },
  },
  watch: {
    chosenRoles(newValue) {
      if (newValue.length > 2) {
        newValue.pop();
      }
    },
  },
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
    isSpecialEducation: false,
    schoolSpecialEdPercentage: 0,
    schoolExtraSpecialEdPercentage: 0,
    matyaSpecialEdPercentage: 0,
    matyaExtraSpecialEdPercentage: 0,
    isGanenet: false,
    kindergardenRole: null,
    kindergardenRoles: [
      "גננת משלימה זכאית",
      "ניהול גן (חדשה באופק)",
      "ניהול גן (ותיקה באופק)",
      "ניהול אשכול",
    ],
    kindergardenSeniority: 0,
    percentageRules: [
      (v) => !!v || "שדה חובה",
      (v) => (v && v > 0 && v <= 150) || "אחוז משרה צריך להיות בין 1-150",
    ],
    specialEdPercentageRules: [
      (v) => (v && v > 0 && v <= 100) || "אחוז משרה צריך להיות בין 1-100",
    ],
    roleRules: [(v) => v.length <= 2 || "לא ניתן לבחור יותר משתי תפקידים"],
  }),
  computed: {
    headers() {
      let role1 = this.chosenRoles[0] || "ללא";
      let role2 = this.chosenRoles[1] || "ללא";
      return [
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
          this.schoolSpecialEdPercentage,
          this.schoolExtraSpecialEdPercentage,
          this.matyaSpecialEdPercentage,
          this.matyaExtraSpecialEdPercentage,
          this.isSpecialEducation,
          "תשפ״ב",
          this.isGanenet,
          this.kindergardenRole,
          this.kindergardenSeniority
        ),
        calculateSalary(
          this.degree,
          this.hinuchComp,
          this.chosenRoles,
          percentage,
          this.seniority,
          this.level,
          this.schoolSpecialEdPercentage,
          this.schoolExtraSpecialEdPercentage,
          this.matyaSpecialEdPercentage,
          this.matyaExtraSpecialEdPercentage,
          this.isSpecialEducation,
          "תשפ״ג",
          this.isGanenet,
          this.kindergardenRole,
          this.kindergardenSeniority
        ),
        calculateSalary(
          this.degree,
          this.hinuchComp,
          this.chosenRoles,
          percentage,
          this.seniority,
          this.level,
          this.schoolSpecialEdPercentage,
          this.schoolExtraSpecialEdPercentage,
          this.matyaSpecialEdPercentage,
          this.matyaExtraSpecialEdPercentage,
          this.isSpecialEducation,
          "תשפ״ד",
          this.isGanenet,
          this.kindergardenRole,
          this.kindergardenSeniority
        ),
      ];
    },
  },
};
</script>
