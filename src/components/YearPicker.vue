<template>
  <picker-scroll :item-list="years" @change="onChange" />
</template>

<script>
import PickerScroll from './PickerScroll.vue';

export default {
  name: 'YearPicker',
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
      years: []
    };
  },
  created() {
    this.init();
  },
  methods: {
    init() {
      const currentYear = new Date().getFullYear();
      const minimumYear = currentYear - 80;
      const maximumYear = currentYear ;

      const years = [];
      // eslint-disable-next-line no-plusplus
      for (let i = minimumYear; i <= maximumYear; i++) {
        years.push({ value: i, name: i, selected: this.value === i });
      }
      this.years = years;
    },
    onChange(value) {
      this.$emit('change', value);
    }
  }
};
</script>
