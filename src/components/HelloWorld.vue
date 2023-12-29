<template>
  <v-container class="fill-height">
    <v-responsive class="align-center fill-height">
    
      <v-row class="d-flex justify-center">
       <v-col
          cols="12"
          sm="6"
        >
          <div :class="['text-body-2']">
            Daily sodium limit (mg)
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
            Meals per day
          </div>

          <v-row>
            <v-col cols="10">
              <v-slider min="1" max="6" v-model="mealsPerDay" step="1" show-ticks hint="The number of meals (including snacks) that you're eating per day. The sodium limit will be equally divided among your meals per day. For example, if you set the sodium limit to 1,500mg per day and you're eating 3 meals a day. The sodium limit for each meal will be 1500/3 = 500mg per meal."></v-slider>
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
          <div :class="['text-body-2']">Estimated number of servings</div>
          
          <v-row>
            <v-col cols="10">
              <v-slider min="1" max="20" v-model="numberOfServings" step="1" show-ticks hint="This is your estimated number of servings for a dish. For example, you're cooking Sinigang (Filipino sour soup dish) and you expect it to feed 6 people. In this case, the number of servings will be 6. Of course, this assumes that each serving will be of equal amounts for each person. This value is used for getting the total sodium limit by means of multiplication."></v-slider>
            </v-col>
            <v-col>
              <div :class="['text-body-1 mt-1']">{{numberOfServings}}</div>
            </v-col>
          </v-row>
        </v-col>
      </v-row>

      <v-row class="d-flex justify-center mb-2">
      
        <v-card
          :title="`Salt: ${saltAmount} pinch.`"
          width="300"
          text="1 pinch = 400mg sodium"
        ></v-card>
      </v-row>

      <v-row class="d-flex justify-center mb-2">
        <v-card
          :title="`Soy Sauce (Toyo): ${soySauceAmount} tbsp.`"
          width="300"
          text="1 tablespoon = 900mg sodium"
        ></v-card>
      </v-row>

      <v-row class="d-flex justify-center mb-2">
        <v-card
          :title="`Fish Sauce (Patis): ${fishSauceAmount} tbsp.`"
          width="300"
          text="1 tablespoon = 1,413 sodium"
        ></v-card>
      </v-row>

      <v-row class="d-flex justify-center mb-2">
        <v-card
          :title="`Ginisa mix: ${ginisaMixAmount} pinch`"
          width="300"
          text="1.3 grams = 260mg sodium"
        ></v-card>
      </v-row>

      <v-row class="d-flex justify-center mb-2">
        <v-card
          :title="`Magic Sarap: ${magicSarapAmount} pinch`"
          width="300"
          text="2 grams = 502mg sodium"
        ></v-card>
      </v-row>


      <v-row class="d-flex justify-center mb-2">
        <v-card
          :title="`Oyster Sauce: ${oysterSauceAmount} tbsp.`"
          width="300"
          text="1 tablespoon = 491mg sodium"
        ></v-card>
      </v-row>

      <v-row class="d-flex justify-center mb-2">
        <v-card
          :title="`Worcestershire Sauce: ${worcestershireSauceAmount} tbsp.`"
          width="300"
          text="1 tablespoon = 166mg sodium"
        ></v-card>
      </v-row>

      <v-row class="d-flex justify-center mb-2">
        <v-card
          :title="`Coco Aminos: ${cocoAminosAmount} tbsp.`"
          width="300"
          text="1 tablespoon = 270mg sodium"
        ></v-card>
      </v-row>

      <v-row class="d-flex justify-center mb-2">
        <v-card
          :title="`Kikkoman soy sauce: ${kikkomanSoySauceAmount} tbsp.`"
          width="300"
          text="1 tablespoon = 960mg sodium"
        ></v-card>
      </v-row>

      <v-row class="d-flex justify-center mb-2">
        <v-card
          :title="`Kikkoman low sodium soy: ${kikkomanLowSodiumSoySauceAmount} tbsp.`"
          width="300"
          text="1 tablespoon = 570mg sodium"
        ></v-card>
      </v-row>

      <v-row class="d-flex justify-center mb-2">
        <v-card
          :title="`Knorr sinigang mix: ${knorrSinigangMixAmount} tsp.`"
          width="300"
          text="1 teaspoon = 948mg sodium"
        ></v-card>
      </v-row>

      <v-row class="d-flex justify-center mb-2">
        <v-card
          :title="`Jufran thai fish sauce: ${jufranThaiFishSauceAmount} tsp.`"
          width="300"
          text="1 teaspoon = 270mg sodium"
        ></v-card>
      </v-row>

    </v-responsive>
  </v-container>
</template>

<style>
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

const getSodiumServing = (sodium_limit_per_meal, sodium_per_serving) => {
  return Math.round((sodium_limit_per_meal / sodium_per_serving) * numberOfServings.value);
}

const saltAmount = computed(() => {
  const sodium_content_per_pinch_of_salt = 400; // mg
  return getSodiumServing(sodiumLimitPerMeal.value, sodium_content_per_pinch_of_salt); 
});

const getPinchSodiumServing = (sodium_limit_per_meal, sodium_per_serving, gram_per_serving) => {
  const grams_per_pinch = 0.36;
  const number_of_servings = sodium_limit_per_meal / sodium_per_serving;
  const total_grams = number_of_servings * gram_per_serving;
  return Math.round(total_grams / grams_per_pinch);
}

const soySauceAmount = computed(() => {
  const sodium_content_per_tablespoon_of_soysauce = 900;
  return getSodiumServing(sodiumLimitPerMeal.value, sodium_content_per_tablespoon_of_soysauce);
});

const ginisaMixAmount = computed(() => {
  const sodium_content_per_serving_of_ginisa_mix = 260;
  const gram_per_serving = 1.3;
  return getPinchSodiumServing(sodiumLimitPerMeal.value, sodium_content_per_serving_of_ginisa_mix, gram_per_serving);
});

const magicSarapAmount = computed(() => {
  const sodium_content_per_serving_of_magic_sarap = 502;
  const gram_per_serving = 2;
  return getPinchSodiumServing(sodiumLimitPerMeal.value, sodium_content_per_serving_of_magic_sarap, gram_per_serving);
});

const fishSauceAmount = computed(() => {
  const sodium_content_per_tablespoon_of_fish_sauce = 1413;
  return getSodiumServing(sodiumLimitPerMeal.value, sodium_content_per_tablespoon_of_fish_sauce);
});

const oysterSauceAmount = computed(() => {
  const sodium_content_per_tablespoon_of_oyster_sauce = 491;
  return getSodiumServing(sodiumLimitPerMeal.value, sodium_content_per_tablespoon_of_oyster_sauce);
});

const worcestershireSauceAmount = computed(() => {
  const sodium_content_per_tablespoon_of_worcestershire_sauce = 166;
  return getSodiumServing(sodiumLimitPerMeal.value, sodium_content_per_tablespoon_of_worcestershire_sauce);
});

const cocoAminosAmount = computed(() => {
  const sodium_content_per_tablespoon_of_coco_aminos = 270;
  return getSodiumServing(sodiumLimitPerMeal.value, sodium_content_per_tablespoon_of_coco_aminos);
});

const kikkomanSoySauceAmount = computed(() => {
  const sodium_content_per_tablespoon_of_kikkoman_soysauce = 960;
  return getSodiumServing(sodiumLimitPerMeal.value, sodium_content_per_tablespoon_of_kikkoman_soysauce);
});

const kikkomanLowSodiumSoySauceAmount = computed(() => {
  const sodium_content_per_tablespoon_of_kikkoman_less_sodium = 590;
  return getSodiumServing(sodiumLimitPerMeal.value, sodium_content_per_tablespoon_of_kikkoman_less_sodium);
});

const knorrSinigangMixAmount = computed(() => {
  const sodium_content_per_teaspoon_of_knorr_sinigang_mix = 948;
  return getSodiumServing(sodiumLimitPerMeal.value, sodium_content_per_teaspoon_of_knorr_sinigang_mix);
});

const jufranThaiFishSauceAmount = computed(() => {
  const sodium_content_per_teaspoon_of_jufran_thai_fish_sauce = 270;
  return getSodiumServing(sodiumLimitPerMeal.value, sodium_content_per_teaspoon_of_jufran_thai_fish_sauce);
});

</script>
