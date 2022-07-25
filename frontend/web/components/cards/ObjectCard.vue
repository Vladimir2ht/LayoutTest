<template>
  <v-card
    v-if="orientation == 'horizontal'"
    class="object__card--horizontal"
  >
    <v-row>
        <v-col class="" style="max-width: 40%">
          <v-img
            max-width="260px"
            lazy-src="/1.jpg"
            src="/1.jpg"
          ></v-img>
        </v-col>
        <v-col class="d-flex align-end flex-column">
          <div class="top-line">
            <v-card-subtitle
              class="align-self-start pa-0"
              >{{ value.type.title }}
            </v-card-subtitle>
            <v-icon>mdi-cards-heart-outline</v-icon>
          </div>
          <v-card-title
            class="align-self-start pt-0 pb-1 pl-0 text-break big-text"
          >
            {{ value.title }}
          </v-card-title>
          <v-card-text class="mt-1 grow">
            <v-row>
              <span class="mr-4">
                {{ maxGuests | word_case(['гость', 'гостя', 'гостей']) }}
              </span>
              <span class="mr-4">
                {{ beads | word_case(['кровать', 'кровати', 'кроватей']) }}
              </span>
              <span class="mr-4">
                {{ beadrooms | word_case(['спальня', 'спальни', 'спален']) }}
              </span>
              <span class="mr-4">
                {{ square }} м2
              </span>
            </v-row>
            <v-row class="options">
              <v-chip
                v-for="item in value.options"
                v-show="item.primary"
                :key="item.id"
                outlined
                :value="item.id"
                class="ml-2"
              >
                <v-icon small left>{{ item.icon }}</v-icon>
                {{ item.title }}
              </v-chip>
            </v-row>
            <v-row>
              <ul>
                <li>Несколько вариантов питания</li>
                <li>Горячее предложение</li>
                <li>Предоплата</li>
              </ul>
            </v-row>
          </v-card-text>
          <v-spacer></v-spacer>
          <v-card-actions
            class="object__card--horizontal__card-actions pb-0 mt-auto d-flex w-100"
          >
            <v-flex class="d-inline-flex align-center">
              <v-icon>mdi-star</v-icon>
              <div class="marks">
                {{ value.rating.value }} <span>({{ value.reviews.count }}<span> отзывов</span>)</span>
              </div>
            </v-flex>
            <v-flex class="text--darken-2 text-right">
              <span class="big-text">от {{ value.price | currency }} / ночь</span>
            </v-flex>
          </v-card-actions>
        </v-col>
      </v-row>
  </v-card>

  <v-card
    v-else-if="orientation == 'vertical'"
    style="width: 350px"
    class="object__card--vertical mx-auto"
    :class="{ 'object__card--map': map }"
  >
    <v-container>
      <v-img
        height="250"
        contain
        lazy-src="https://cdn.vuetifyjs.com/images/cards/cooking.png"
        src="https://cdn.vuetifyjs.com/images/cards/cooking.png"
        :class="{ 'object__card__image--map': map }"
      >
      </v-img>
      <v-card-subtitle
        class="text--secondary text-caption pt-1 pb-0"
        >{{ value.type.title }}</v-card-subtitle
      >
      <v-card-title
        class="text-h6 pt-0 pb-5 text-break"
      >
        {{ value.title }}
      </v-card-title>
      <v-card-text>
        <v-row align="center" style="width: 90%">
          <div class="text-caption">
            {{ value.rating.value }} ({{ value.reviews.count }})
          </div>
          <div
            class="text--darken-2 text--secondary text-h6 text-right ml-auto"
          >
            <span>{{ value.price | currency }}</span>
          </div>
        </v-row>
      </v-card-text>
      <v-divider class="mx-4"></v-divider>
    </v-container>
  </v-card>
</template>

<script>
import collect from 'collect.js'

export default {
  name: 'ObjectCard',
  props: {
    orientation: {
      // horizontal || vertical
      type: String,
      default: 'horizontal',
    },
    value: {
      type: Object,
      default: () => ({
        id: '1',
        title: 'Уютная однокомнатная квартира с видом на море',
        price: 4500,
        type: {
          id: 1,
          name: 'flat',
          title: 'Квартира',
        },
        params: [
          { id: '1', name: 'square', title: 'Площадь', value: '40' },
          { id: '2', name: 'beads', title: 'Количество комнат', value: '2' },
          { id: '3', name: 'beadrooms', title: 'Количество спален', value: '1' },
          {
            id: '4',
            name: 'max_guests',
            title: 'Kоличество гостей',
            value: '4',
          },
        ],
        options: [
          {
            id: '1',
            name: 'wifi',
            title: 'WI-FI',
            order: 0,
            primary: true,
            icon: 'mdi-wifi',
          },
          {
            id: '2',
            name: 'pets',
            title: 'Pets friendly',
            order: 1,
            primary: true,
            icon: 'mdi-waves',
          },
          {
            id: '3',
            name: 'reset',
            title: 'Бесплатная отмена',
            order: 2,
            primary: true,
            icon: 'mdi-compass-rose',
          },
        ],
        rating: {
          value: '4,75',
          detail: {},
        },
        reviews: {
          count: 8,
          list: [],
        },
      }),
    },
    map: {
      type: Boolean,
      default: false,
    },
  },
  computed: {
    /**
     * Collection of the value
     * @return {Collection<unknown>}
     */
    val() {
      return collect(this.value)
    },
    params() {
      return collect(this.val.get('params')).keyBy('name')
    },
    square() {
      return this.params.get('square').value
    },
    beads() {
      return this.params.get('beads').value
    },
    beadrooms() {
      return this.params.get('beadrooms').value
    },
    maxGuests() {
      return this.params.get('max_guests').value
    },
  },
  methods: {},
}
</script>

<style>
.object__card--map .v-image .v-image__image--preload {
  filter: none !important;
}
.object__card__image--map .v-image .v-image__image--preload {
  filter: none !important;
}
.object__card--horizontal__card-actions {
  width: 100%;
}
</style>
