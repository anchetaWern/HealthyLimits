<template>
  <v-container class="fill-height">
    <v-responsive class="align-center fill-height">

      <div :class="['alert-container mb-3', 'text-body-2']">
        <v-alert type="info" text="" closable>
          <div class="text-subtitle-1 font-weight-medium">How to use</div>
          <ol>
            <li>1. Set your desired sodium limit. If you want to stay within the healthy range, this should be between 1,500mg to 2,300mg per day.</li>
            <li>2. Set how many times do you eat per day (including snacks).</li>
            <li>3. Set the estimated number of servings your dish can make. This should be equivalent to the number of people your dish can feed. This assumes each serving is of equal amount.</li>
          </ol>
        </v-alert>
      </div>

      <v-row class="d-flex justify-center">
       <v-col
          cols="12"
          sm="6"
        >
          <div :class="['text-body-2']">
            1. Daily sodium limit (mg)
          </div>

          <v-text-field
            v-model="sodiumLimit"
            label=""
            variant="solo"
            type="number"
            hint="The American Heart Association recommends no more than 2,300 milligrams (mgs) a day and an ideal limit of no more than 1,500 mg per day."
          ></v-text-field>
        </v-col>
      </v-row>

      <v-row class="d-flex justify-center">
       <v-col
          cols="12"
          sm="6"
        >
          <div :class="['text-body-2']">
            2. Meals per day
          </div>

          <v-row>
            <v-col cols="10">
              <div class="pr-1 pl-3">
                <v-slider min="1" max="6" v-model="mealsPerDay" step="1" show-ticks hint="The number of meals (including snacks) that you're eating per day. The sodium limit will be equally divided among your meals per day. For example, if you set the sodium limit to 1,500mg per day and you're eating 3 meals a day. The sodium limit for each meal will be 1500/3 = 500mg per meal."></v-slider>
              </div>
            </v-col>
            <v-col>
              <div :class="['text-body-1 mt-1']">{{mealsPerDay}}</div>
            </v-col>
          </v-row>
          
        </v-col>
      </v-row>

      <v-row class="d-flex justify-center">
       <v-col
          cols="12"
          sm="6"
        >
          <div :class="['text-body-2']">3. Estimated number of servings</div>
          
          <v-row>
            <v-col cols="10">
              <div class="pl-3">
                <v-slider min="1" max="20" v-model="numberOfServings" step="1" show-ticks hint="This is your estimated number of servings for a dish. For example, you're cooking Sinigang (Filipino sour soup dish) and you expect it to feed 6 people. In this case, the number of servings will be 6. Of course, this assumes that each serving will be of equal amounts for each person. This value is used for getting the total sodium limit by means of multiplication."></v-slider>
              </div>
            </v-col>
            <v-col>
              <div :class="['text-body-1 mt-1']">{{numberOfServings}}</div>
            </v-col>
          </v-row>
        </v-col>
      </v-row>

      <v-row class="d-flex justify-center">
        <v-col cols="10">
          <div :class="['text-h6 mt-1']">Seasoning amount for your dish</div>
        </v-col>
      </v-row>

      <v-row class="d-flex justify-end mb-2">
        <v-col cols="4">
          <v-btn size="small" @click="resetPercent">Reset</v-btn>
        </v-col>
      </v-row>

      <v-row class="d-flex justify-center mb-2">
        <v-card
          :title="`${Math.round(totalSodiumPerServing)}mg`"
          text="Total sodium per serving"
          width="300"
        >
         
        </v-card>
      </v-row>

      <v-row class="d-flex justify-center mb-2" v-for="(item, index) in seasonings">
        <v-card
          :title="`${item.title.replace('[amount]', computedServing(item))}`"
          width="300"
          :text="item.text"
        >
          <v-row>
            <v-col cols="9">
              <div class="pr-1 pl-3">
                <v-slider v-model="item.percentage" min="0" max="100" step="5" show-ticks hint=""></v-slider>
              </div>
            </v-col>
            <v-col>
              <div :class="['text-body-2 mt-1']">{{item.percentage}}%</div>
            </v-col>
          </v-row>

        </v-card>
      </v-row>


    </v-responsive>
  </v-container>
</template>

<style>
ol li {
  margin-bottom: 10px;
}

.alert-container {
  line-height: 18px;
}

.v-card-title {
  font-size: 1rem !important;
}
</style>

<script setup>
import { ref, computed, watch } from 'vue';

const sodiumLimit = ref(1500);
const mealsPerDay = ref(3);

const sodiumLimitPerMeal = computed(() => {
  return Math.round(sodiumLimit.value / mealsPerDay.value);
});

const numberOfServings = ref(5);

const getSodiumServing = (sodium_limit_per_meal, sodium_per_serving, multiplier = 1) => {
  return Math.round((sodium_limit_per_meal / sodium_per_serving) * numberOfServings.value) * multiplier;
}

const getPinchSodiumServing = (sodium_limit_per_meal, sodium_per_serving, gram_per_serving) => {
  const grams_per_pinch = 0.36;
  const number_of_servings = sodium_limit_per_meal / sodium_per_serving;
  const total_grams = number_of_servings * gram_per_serving;
  return Math.round(total_grams / grams_per_pinch);
}

const computedServing = (item) => {
  const sodiumLimitForSeasoning = item.percentage === 0 ? sodiumLimitPerMeal.value : (sodiumLimitPerMeal.value * item.percentage) / 100;

  if (item.method === 'pinch') {
    return getPinchSodiumServing(sodiumLimitForSeasoning, item.sodium_content_per_serving, item.gram_per_serving);
  } 
  const multi = item.multiplier ? item.multiplier : 1;
  return getSodiumServing(sodiumLimitForSeasoning, item.sodium_content_per_unit, multi); 
};



const seasonings = ref([
  {
    title: `Salt: [amount] pinch.`,
    text: '1 pinch = 400mg sodium',
    sodium_content_per_unit: 400,
    percentage: ref(0),
  },
  {
    title: `Soy Sauce (Toyo): [amount] tbsp.`,
    text: '1 tablespoon = 900mg sodium',
    sodium_content_per_unit: 900,
    percentage: ref(0),
  },
  {
    title: `Fish Sauce (Patis): [amount] tbsp.`,
    text: '1 tablespoon = 1,413 sodium',
    sodium_content_per_unit: 1413,
    percentage: ref(0),
  },
  {
    title: `Ajinomoto Ginisa mix: [amount] pinch`,
    text: '1.3 grams = 260mg sodium',
    sodium_content_per_serving: 260,
    gram_per_serving: 1.3,
    method: 'pinch',
    percentage: ref(0),
  },
  {
    title: `Maggi Magic Sarap: [amount] pinch`,
    text: '2 grams = 502mg sodium',
    sodium_content_per_serving: 502,
    gram_per_serving: 2,
    method: 'pinch',
    percentage: ref(0),
  },

  {
    title: `Oyster Sauce: [amount] tbsp.`,
    text: '1 tablespoon = 491mg sodium',
    sodium_content_per_unit: 491,
    percentage: ref(0),
  },

  {
    title: `Worcestershire Sauce: [amount] tbsp.`,
    text: '1 tablespoon = 166mg sodium',
    sodium_content_per_unit: 166,
    percentage: 0,
  },

  {
    title: `Coco Aminos: [amount] tbsp.`,
    text: '1 tablespoon = 270mg sodium',
    sodium_content_per_unit: 270,
    percentage: ref(0),
  },

  {
    title: `Kikkoman soy sauce: [amount] tbsp.`,
    text: '1 tablespoon = 960mg sodium',
    sodium_content_per_unit: 960,
    percentage: ref(0),
  },
  {
    title: `Kikkoman low sodium soy: [amount] tbsp.`,
    text: '1 tablespoon = 570mg sodium',
    sodium_content_per_unit: 570,
    percentage: ref(0),
  },
  {
    title: `Knorr sinigang mix: [amount] pinch`,
    text: '2.8 grams = 474mg sodium',
    sodium_content_per_serving: 474,
    gram_per_serving: 2.8,
    method: 'pinch',
    percentage: ref(0),
  },

  {
    title: `Jufran thai fish sauce: [amount] tsp.`,
    text: '1 teaspoon = 270mg sodium',
    sodium_content_per_unit: 270,
    percentage: ref(0),
  },

  {
    title: `Knorr cubes: [amount] cube`,
    text: '1/2 cube = 1,110mg sodium',
    sodium_content_per_unit: 1110,
    multiplier: 0.5,
    percentage: ref(0),
  }
  
]);

const totalSodiumPerServing = computed(() => {
 
  const selectedSeasonings = seasonings.value.filter((item) => {
    return item.percentage > 0;
  });

  const sodiumPerSeasoning = selectedSeasonings.map((item) => {
    return (computedServing(item) * item.sodium_content_per_unit);
  });

  var totalSodium = sodiumPerSeasoning.reduce((accumulator, currentValue) => {
    return accumulator + currentValue
  },0);
  
  return Math.round(totalSodium / numberOfServings.value);
});


const resetPercent = () => {
  seasonings.value.forEach((item) => {
    item.percentage = 0;
  });
}
</script>
