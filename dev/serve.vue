<script>
import Vue from 'vue';
import VueDatepickerScroll from '@/vue-datepicker-scroll.vue';

export default Vue.extend({
  name: 'ServeDev',
  components: {
    VueDatepickerScroll
  }
});
</script>

<template>
  <div id="app">
    <vue-datepicker-scroll />
  </div>
</template>
