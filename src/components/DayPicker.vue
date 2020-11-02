<template>
  <picker-scroll :item-list="days" @change="onChange" />
</template>

<script>
import PickerScroll from './PickerScroll.vue';

export default {
  name: 'DayPicker',
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
      days: []
    };
  },
  created() {
    this.init();
  },
  methods: {
    init() {
      const days = [];
      // eslint-disable-next-line no-plusplus
      for (let i = 1; i <= 31; i++) {
        days.push({ value: i, name: i, selected: this.value === i });
      }
      this.days = days;
    },
    onChange(value) {
      this.$emit('change', value);
    }
  }
};
</script>
