<template>
  <div class="vds">
    <div class="vds-input-group">
      <div class="vds-addon">
        <Icon />
      </div>
      <input
        id="birthday"
        type="text"
        :class="computedInputClass"
        inputmode="numeric"
        :placeholder="placeHolder"
        data-testid="input-picker"
        autocomplete="off"
        readonly="readonly"
        :disabled="disabled"
        :value="selectedDate"
        @click="togglePicker"
      />
    </div>
    <transition name="modal" data-testid="transition">
      <div v-if="isOpenPicker" class="modal-mask" data-testid="picker-modal">
        <div class="modal-wrapper">
          <div class="modal-body">
            <day-picker :value="day" data-testid="day-picker" @change="selectDay" />
            <month-picker
              :value="month"
              data-testid="month-picker"
              class="month-picker"
              @change="selectMonth"
            />
            <year-picker :value="year" data-testid="year-picker" @change="selectYear" />
          </div>
          <div class="modal-footer">
            <slot name="footer">
              <button
                type="button"
                class="btn btn-ok"
                data-testid="confirm"
                @click="onConfirm"
              >
                Ok
              </button>
              <button
                type="button"
                class="btn btn-cancel"
                data-testid="cancel"
                @click="onCancel"
              >
                Cancel
              </button>
            </slot>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import '@/assets/css/vue-datepicker-scroll.css';
import Icon from '@/components/Icon';
import YearPicker from "@/components/YearPicker";
import MonthPicker from "@/components/MonthPicker";
import DayPicker from "@/components/DayPicker";

export default {
  name: 'VueDatepickerScroll',
  components: {
    YearPicker,
    MonthPicker,
    DayPicker,
    Icon
  },
  props: {
    inputClass: {
      type: [String, Object],
      default: ''
    },
    value: {
      type: [Date, String],
      default: null
    },
    placeHolder: {
      type: String,
      default: ''
    },
    disabled: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      selectedDate: this.value,
      isOpenPicker: false,
      day: 1,
      month: 0,
      year: 2483
    };
  },
  computed: {
    computedInputClass() {
      if (typeof this.inputClass === 'string') {
        return [this.inputClass, 'vds-form-control'].join(' ');
      }
      return { 'vds-form-control': true, ...this.inputClass };
    }
  },
  created() {
    if (this.value && typeof this.value === 'string') {
      const [day, month, year] = this.value.split('/');
      this.day = Number(day.replace('^0+', ''));
      this.month = Number(month.replace('^0+', '')) - 1;
      this.year = Number(year);
    }
  },
  methods: {
    onInput($event) {
      this.selectedDate = $event.target.value;
      this.$emit('input', $event.target.value);
      this.$emit('change', $event.target.value);
    },
    togglePicker() {
      this.isOpenPicker = !this.isOpenPicker;
      this.handleBodyScroll();
    },
    onConfirm() {
      this.parseDate();
      this.$emit('input', this.selectedDate);
      this.$emit('change', this.selectedDate);
      this.closePicker();
    },
    onCancel() {
      this.closePicker();
      this.$emit('close');
    },
    closePicker() {
      this.isOpenPicker = false;
      this.handleBodyScroll();
    },
    selectMonth(month) {
      this.month = month;
    },
    selectDay(day) {
      this.day = day;
    },
    selectYear(year) {
      this.year = year;
    },
    parseDate() {
      const date = new Date(this.year, this.month, this.day);
      const day = date
          .getDate()
          .toString()
          .padStart(2, '0');
      const month = (date.getMonth() + 1).toString().padStart(2, '0');
      this.selectedDate = `${day}/${month}/${date.getFullYear()}`;
      this.$emit('change', date);
    },
    handleBodyScroll() {
      document.body.classList.toggle('overflow-hidden');
    }
  }
};
</script>
