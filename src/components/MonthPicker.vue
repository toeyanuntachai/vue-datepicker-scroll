<template>
  <picker-scroll :item-list="months" @change="onChange" />
</template>

<script>
import PickerScroll from './PickerScroll.vue';
import { monthEN, monthTH } from '../constant';

export default {
  name: 'MonthPicker',
  components: {
    PickerScroll
  },
  props: {
    value: {
      type: Number,
      default: null
    }
  },
  data() {
    return {
      months: []
    };
  },
  created() {
    this.init();
  },
  methods: {
    init() {
      const months = this.getMonthsByLocale();

      this.months = months.reduce((result, value, index) => {
        return [...result, { value: index, name: value, selected: this.value === index }];
      }, []);
    },
    getMonthsByLocale() {
      return monthEN;
    },
    onChange(value) {
      this.$emit('change', value);
    }
  }
};
</script>
