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
            :rules="seniorityRules"
          ></v-text-field>
          <v-text-field
            v-model="level"
            type="number"
            min="1"
            max="9"
            label="דרגה אופק חדש"
            required
            :rules="levelRules"
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
          <v-switch
            v-model="isSpecialEducationNoDiploma"
            label="משרה חנ״מ למי שאין תעודת חנ״מ"
          >
          </v-switch>
          <v-switch v-model="isGiftedEducation" label="חינוך מחוננים">
          </v-switch>
          <v-container v-if="isGiftedEducation">
            <v-text-field
              v-model="schoolGiftedEdPercentage"
              type="number"
              min="0"
              max="100"
              label="% משרה מחוננים בבי״ס"
              required
              :rules="specialEdPercentageRules"
            ></v-text-field>
            <v-text-field
              v-model="schoolExtraGiftedEdPercentage"
              type="number"
              min="0"
              max="100"
              label="% משרה מחוננים אקסטרה"
              required
              :rules="specialEdPercentageRules"
            ></v-text-field>
          </v-container>
          <v-switch v-model="isIntern" label="ליווי סטאז׳רים"> </v-switch>
          <v-container v-if="isIntern">
            <v-text-field
              v-model="numberOfStudentsInternship"
              type="number"
              min="0"
              max="3"
              label="מספר הסטודנטים"
              required
              :rules="numberOfStudentsRules"
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
              min="1"
              max="10"
              v-model="kindergardenSeniority"
              label="וותק בניהול"
              required
            ></v-text-field>
          </v-container>
          <v-btn :disabled="!valid" color="success" class="mr-4"> חשב </v-btn>
          <v-btn @click="reset" color="warning" class="mr-4"> אפס </v-btn>
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
  "גמול אחר (6%)": 6 / 100,
  "ריכוז מקצוע חט״ב": 6 / 100,
  "רכז ליקויי למידה": 4 / 100,
  "ריכוז מעבדה": 3 / 100,
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
  "גמול אחר (6%)": 6 / 100,
  "ריכוז מקצוע חט״ב": 6 / 100,
  "רכז ליקויי למידה": 4 / 100,
  "ריכוז מעבדה": 3 / 100,
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
  "גמול אחר (6%)": 0,
  "ריכוז מקצוע חט״ב": 0,
  "רכז ליקויי למידה": 0,
  "ריכוז מעבדה": 0,
};

// כיתה א׳
const hinuchA = 11.5 / 100;

// כיתה ב׳-ט׳
const hinuchRest = 10 / 100;

// same for both options (א-ט׳)
const finiteCompHinuch = 1000;

function limitValue(value, lowerBound, upperBound) {
  return Math.min(Math.max(value, lowerBound), upperBound);
}

function calcRoleCompensation(
  year,
  role,
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

function calculateSpecialEdNoDiploma(
  year,
  twentytwoAddition,
  mixedCompensation
) {
  let salary = mixedCompensation + twentytwoAddition;
  if (year == "תשפ״ב" || year == "תשפ״ג") {
    return salary * (8.5 / 100);
  } else if (year == "תשפ״ד") {
    return salary * (8.5 / 100);
  }
}

function calculateAccompanyingInterns(
  numberOfStudentsInternship,
  year,
  twentytwoAddition,
  mixedCompensation
) {
  let salary = mixedCompensation + twentytwoAddition;
  if (year == "תשפ״ב" || year == "תשפ״ג") {
    return salary * (2.4 / 100) * numberOfStudentsInternship;
  } else if (year == "תשפ״ד") {
    return salary * (2.4 / 100) * numberOfStudentsInternship;
  }
}

function calculateGiftedEd(
  school,
  extraSchool,
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

  if (year != "תשפ״ד") {
    return (
      salary * schoolBonusThisYear * school +
      salary * extraSchoolBonusThisYear * extraSchool
    );
  } else {
    return (
      salary * schoolBonusNextYear * school +
      salary * extraSchoolBonusNextYear * extraSchool
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
    let optA = kinderLevelComp[level - 1] * calcBase;
    if (year == "תשפ״ד") {
      return Math.max(optA, 1500);
    }
    return optA;
  } else if (kindergardenRole == "ניהול גן (ותיקה באופק)") {
    let optA = kinderSeniorityComp[kindergardenSeniority - 1] * calcBase;
    if (year == "תשפ״ד") {
      return Math.max(optA, 1500);
    }
    return optA;
  }
  return 0;
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
  isSpecialEducationNoDiploma,
  schoolGiftedEdPercentage,
  schoolExtraGiftedEdPercentage,
  isGiftedEducation,
  jewishYear,
  isGanenet,
  isIntern,
  numberOfStudentsInternship,
  kindergardenRole,
  kindergardenSeniority
) {
  var base;
  var additionRaw;
  if (jewishYear == "תשפ״ב") {
    base = thisJewishYearBases[degree];
    additionRaw = 0;
  } else {
    base = nextJewishYearBases[degree];
    if (degree == "תואר ראשון") {
      additionRaw = firstDegreeAddition[level - 1];
    } else {
      additionRaw = secondDegreeAddition[level - 1];
    }
  }
  if (jewishYear == "תשפ״ג") {
    additionRaw /= 2;
  }
  var addition = additionRaw * percentage;

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
    additionRaw,
    mixedCompensationRaw
  );
  var roleCompensation2 = calcRoleCompensation(
    jewishYear,
    roles[1] || "ללא",
    additionRaw,
    mixedCompensationRaw
  );

  var hinuchCompensation = 0;
  if (hinuchComp != "ללא") {
    let hinuchVariable = hinuchComp == "כיתה א׳" ? hinuchA : hinuchRest;
    hinuchCompensation = calculateHinuch(
      additionRaw,
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
  var specialEdNoDiplomaComp = 0;
  if (isSpecialEducationNoDiploma) {
    specialEdNoDiplomaComp = calculateSpecialEdNoDiploma(
      jewishYear,
      addition,
      mixedCompensation
    );
  }
  var giftedEdComp = 0;
  if (isGiftedEducation) {
    giftedEdComp = calculateGiftedEd(
      schoolGiftedEdPercentage / 100,
      schoolExtraGiftedEdPercentage / 100,
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
      additionRaw
    );
  }
  var internshipComp = 0;
  if (isIntern) {
    internshipComp = calculateAccompanyingInterns(
      numberOfStudentsInternship,
      jewishYear,
      addition,
      mixedCompensation
    );
  }
  let data = {
    mixedCompensation: Math.round(mixedCompensation),
    shiklitAddition: Math.round(shiklitAddition),
    twentytwoAddition: Math.round(addition),
    phoneReimbursement: Math.round(limitValue(percentage, 0.01, 1) * 48.6),
    hinuchCompensation: Math.round(hinuchCompensation),
    roleCompensation1: Math.round(roleCompensation1),
    roleCompensation2: Math.round(roleCompensation2),
    specialEducationCompensation: Math.round(specialEdComp),
    specialEducationNoDiplomaCompensation: Math.round(specialEdNoDiplomaComp),
    giftedEducationCompensation: Math.round(giftedEdComp),
    accompanyingInterns: Math.round(internshipComp),
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
      this.isSpecialEducationNoDiploma = false;
      this.isGiftedEducation = false;
      this.schoolSpecialEdPercentage = 0;
      this.schoolExtraSpecialEdPercentage = 0;
      this.matyaSpecialEdPercentage = 0;
      this.matyaExtraSpecialEdPercentage = 0;
      this.schoolGiftedEdPercentage = 0;
      this.schoolExtraGiftedEdPercentage = 0;
      this.jewishYear = "תשפ״ב";
      this.kindergardenRole = null;
      this.isGanenet = false;
      this.isIntern = false;
      this.numberOfStudentsInternship = 0;
      this.kindergardenSeniority = 1;
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
      "גמול אחר (6%)",
      "ריכוז מקצוע חט״ב",
      "רכז ליקויי למידה",
      "ריכוז מעבדה",
    ],
    chosenRoles: [],
    percentage: 100,
    seniority: 1,
    level: 1,
    isSpecialEducation: false,
    isSpecialEducationNoDiploma: false,
    isGiftedEducation: false,
    schoolSpecialEdPercentage: 0,
    schoolExtraSpecialEdPercentage: 0,
    matyaSpecialEdPercentage: 0,
    matyaExtraSpecialEdPercentage: 0,
    schoolGiftedEdPercentage: 0,
    schoolExtraGiftedEdPercentage: 0,
    isGanenet: false,
    isIntern: false,
    numberOfStudentsInternship: 0,
    kindergardenRole: null,
    kindergardenRoles: [
      "גננת משלימה זכאית",
      "ניהול גן (חדשה באופק)",
      "ניהול גן (ותיקה באופק)",
      "ניהול אשכול",
    ],
    kindergardenSeniority: 1,
    percentageRules: [
      (v) => !!v || "שדה חובה",
      (v) => (v && v > 0 && v <= 150) || "אחוז משרה צריך להיות בין 1-150",
    ],
    specialEdPercentageRules: [
      (v) => (v && v >= 0 && v <= 100) || "אחוז משרה צריך להיות בין 0-100",
    ],
    numberOfStudentsRules: [
      (v) => (v && v >= 0 && v <= 3) || "מספר התלמידים צריך להיות בין 0 ל3",
    ],
    levelRules: [(v) => (v && v >= 1 && v <= 9) || "דרגה בין 1 ל-9"],
    seniorityRules: [(v) => (v && v >= 1 && v <= 36) || "ותק בין 1 ל-36"],
    roleRules: [(v) => v.length <= 2 || "לא ניתן לבחור יותר משני תפקידים"],
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
        {
          text: "גמול חנ״מ ללא תעודה",
          value: "specialEducationNoDiplomaCompensation",
        },
        { text: "גמול חינוך מחוננים", value: "giftedEducationCompensation" },
        { text: "גמול ליווי סטאז׳רים", value: "accompanyingInterns" },
        { text: "גמול גננות", value: "kindergardenCompensation" },
        { text: "סה״כ ברוטו", value: "totalCompensation" },
      ];
    },
    compensations() {
      let percentage = limitValue(this.percentage, 1, 150) / 100.0;
      let seniority = limitValue(this.seniority, 1, 36);
      let level = limitValue(this.level, 1, 9);
      let kindergardenSeniority = limitValue(this.kindergardenSeniority, 1, 10);
      let schoolSpecialEdPercentage = limitValue(
        this.schoolSpecialEdPercentage,
        0,
        100
      );
      let schoolExtraSpecialEdPercentage = limitValue(
        this.schoolExtraSpecialEdPercentage,
        0,
        100
      );
      let matyaSpecialEdPercentage = limitValue(
        this.matyaSpecialEdPercentage,
        0,
        100
      );
      let matyaExtraSpecialEdPercentage = limitValue(
        this.matyaExtraSpecialEdPercentage,
        0,
        100
      );
      let schoolGiftedEdPercentage = limitValue(
        this.schoolGiftedEdPercentage,
        0,
        100
      );
      let schoolExtraGiftedEdPercentage = limitValue(
        this.schoolExtraGiftedEdPercentage,
        0,
        100
      );
      let numberOfStudentsInternship = limitValue(
        this.numberOfStudentsInternship,
        0,
        3
      );
      return [
        calculateSalary(
          this.degree,
          this.hinuchComp,
          this.chosenRoles,
          percentage,
          seniority,
          level,
          schoolSpecialEdPercentage,
          schoolExtraSpecialEdPercentage,
          matyaSpecialEdPercentage,
          matyaExtraSpecialEdPercentage,
          this.isSpecialEducation,
          this.isSpecialEducationNoDiploma,
          schoolGiftedEdPercentage,
          schoolExtraGiftedEdPercentage,
          this.isGiftedEducation,
          "תשפ״ב",
          this.isGanenet,
          this.isIntern,
          numberOfStudentsInternship,
          this.kindergardenRole,
          kindergardenSeniority
        ),
        calculateSalary(
          this.degree,
          this.hinuchComp,
          this.chosenRoles,
          percentage,
          seniority,
          level,
          schoolSpecialEdPercentage,
          schoolExtraSpecialEdPercentage,
          matyaSpecialEdPercentage,
          matyaExtraSpecialEdPercentage,
          this.isSpecialEducation,
          this.isSpecialEducationNoDiploma,
          schoolGiftedEdPercentage,
          schoolExtraGiftedEdPercentage,
          this.isGiftedEducation,
          "תשפ״ג",
          this.isGanenet,
          this.isIntern,
          numberOfStudentsInternship,
          this.kindergardenRole,
          kindergardenSeniority
        ),
        calculateSalary(
          this.degree,
          this.hinuchComp,
          this.chosenRoles,
          percentage,
          seniority,
          level,
          schoolSpecialEdPercentage,
          schoolExtraSpecialEdPercentage,
          matyaSpecialEdPercentage,
          matyaExtraSpecialEdPercentage,
          this.isSpecialEducation,
          this.isSpecialEducationNoDiploma,
          schoolGiftedEdPercentage,
          schoolExtraGiftedEdPercentage,
          this.isGiftedEducation,
          "תשפ״ד",
          this.isGanenet,
          this.isIntern,
          numberOfStudentsInternship,
          this.kindergardenRole,
          kindergardenSeniority
        ),
      ];
    },
  },
};
</script>
