<template>
  <v-form ref="search" v-model="valid" lazy-validation @submit.prevent="submit">
    <div class="date-menu">
      <v-icon color="#00ACA2">mdi-magnify</v-icon>

        <v-tooltip v-model="showCityHint" :top="!short" :bottom="short">
          <template #activator="{}">
            <map-city-select ref="citySelect" v-model="city" :dense="short"></map-city-select>
          </template>
          <!-- <v-icon dense color="accent">mdi-home-search</v-icon> -->
          <span> Пункт назначения </span>
        </v-tooltip>
      <div class="vertical-line"></div>
        <v-menu
          ref="menuDateFrom"
          v-model="menuDateFrom"
          :close-on-content-click="false"
          transition="scale-transition"
          offset-y
          max-width="290px"
          min-width="auto"
        >
          <template #activator="{ on, attrs }">
            <v-text-field
              v-model="dateFromFormatted"
              :prepend-icon="short ? '' : 'mdi-calendar'"
              v-bind="attrs"
              filled
              solo
              :dense="short"
              @blur="dateFrom = parseDate(dateFromFormatted)"
              v-on="on"
              class="date-input"
            ></v-text-field>
          </template>
          <v-date-picker
            v-model="dateFrom"
            no-title
            @input="menuDateFrom = false"
          ></v-date-picker>
        </v-menu>-
        <v-menu
          ref="menuDateTo"
          v-model="menuDateTo"
          :close-on-content-click="false"
          transition="scale-transition"
          offset-y
          max-width="290px"
          min-width="auto"
        >
          <template #activator="{ on, attrs }">
            <v-text-field
              v-model="dateToFormatted"
              :prepend-icon="short ? '' : 'mdi-calendar'"
              v-bind="attrs"
              filled
              solo
              :dense="short"
              @blur="dateTo = parseDate(dateToFormatted)"
              v-on="on"
              class="date-input"
              style="text-align: right;"
            ></v-text-field>
          </template>
          <v-date-picker
            v-model="dateTo"
            no-title
            @input="menuDateTo = false"
          ></v-date-picker>
        </v-menu>
      <div class="vertical-line"></div>
      <div>3 гостя, 1 питомец</div>
    </div>
  </v-form>
</template>

<script>

import { mapMutations } from "vuex";
import MapCitySelect from "../../controls/selects/MapCitySelect";
const DATE_FORMAT = 'D.MM'

export default {
  name: 'SearchForm',
  components: {MapCitySelect},

  props: {
    short: {
      type: Boolean,
      default: false,
    },
  },
  data: (vm) => ({
    valid: false,

    dateFrom: null,
    dateTo: null,
    city: vm.$route.params.city,

    dateFromFormatted: !vm.dateFrom ? '' : vm.formatDate(vm.dateFrom),
    dateToFormatted: !vm.dateTo ? '' : vm.formatDate(vm.dateTo),
    menuDateFrom: false,
    menuDateTo: false,

    showCityHint: false,
  }),
  computed: {
    computedDateFormatted() {
      return this.formatDate(this.date)
    },
  },

  watch: {
    dateFrom(val) {
      this.dateFromFormatted = this.formatDate(this.dateFrom)
    },
    dateTo(val) {
      this.dateToFormatted = this.formatDate(this.dateTo)
    },
  },

  methods: {
    ...mapMutations({
      'setCurrentCity' : 'web/geo/setCity'
    }),
    onCityChange(value){
      this.showCityHint = false;
      this.setCurrentCity(value);
      if(this.short){
        this.submit();
      }
    },

    submit() {
      if (!this.city) {
        this.showCityHint = true
        this.$refs.citySelect.activateMenu();
        return
      }
      const url = `/search/${this.city}/`
      this.$router.push(url)
    },
    formatDate(date) {
      if (!date) return null
      return this.$dayjs(date).format(DATE_FORMAT)
    },
    parseDate(date) {
      if (!date) return null
      return this.$dayjs(date, DATE_FORMAT).format('YYYY-MM-DD')
    },
  },
}
</script>

<style scoped></style>
