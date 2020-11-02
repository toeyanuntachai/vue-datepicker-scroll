<template>
  <div>
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
    <transition name="modal" data-testid="transition">
      <div v-if="isOpenPicker" class="modal-mask" data-testid="picker-modal">
        <div class="modal-wrapper">
          <div class="modal-container p-0">
            <div class="modal-body p-0 m-0">
              <day-picker :value="day" data-testid="day-picker" @change="selectDay" />
              <month-picker
                  :value="month"
                  data-testid="month-picker"
                  class="month-picker"
                  @change="selectMonth"
              />
              <year-picker :value="year" data-testid="year-picker" @change="selectYear" />
            </div>

            <div class="modal-footer d-flex p-0">
              <slot name="footer">
                <button
                    type="button"
                    class="btn btn-block border-0 m-0 p-0 rounded-0"
                    data-testid="confirm"
                    @click="onConfirm"
                >
                  <i class="far fa-check"></i>
                </button>
                <button
                    type="button"
                    class="btn btn-block border-0 m-0 p-0 rounded-0"
                    data-testid="cancel"
                    @click="onCancel"
                >
                  <i class="far fa-times"></i>
                </button>
              </slot>
            </div>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import YearPicker from "@/components/YearPicker";
import MonthPicker from "@/components/MonthPicker";
import DayPicker from "@/components/DayPicker";

export default {
  name: 'VueDatepickerScroll',
  components: {
    YearPicker,
    MonthPicker,
    DayPicker
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
        return [this.inputClass, 'form-control'].join(' ');
      }
      return { 'form-control': true, ...this.inputClass };
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

<style scoped>
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  position: relative;
  display: flex;
  align-items: center;
  margin: auto;
  height: 100vh;
  @media (min-width: 992px) {
    width: 744px;
    left: calc(280px / 2);
  }
}

.modal-container {
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  width: 280px;
  @media (min-width: 375px) {
    width: 345px;
  }
  ::v-deep {
    .vscroller {
      padding: 0;
    }
    .vscroller__body {
      &::before {
        background-color: #a2acb3;
        height: 40px;
      }
      &::after {
        content: none;
      }
      li.vselected {
        font-weight: normal;
      }
      li {
        cursor: grab;
      }
    }
    .btn {
      display: flex;
      align-items: center;
      justify-content: center;
      height: 47px;
      i {
        color: #767676;
        font-size: 22px;
        font-weight: 700;
      }
      &:focus {
        box-shadow: none;
      }
    }
  }
  .month-picker {
    border-left: 2px solid #d4dde2;
    border-right: 2px solid #d4dde2;
  }
}

.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal-body {
  margin: 20px 0;
  display: flex;
  background-color: #f2f2f2;
  max-height: 365px;
}
.modal-footer {
  background-color: #ebebeb;
  height: 47px;
  border-radius: 0;
  border-top: 1px solid #d4dde2;
  position: relative;
  &::before {
    content: '';
    display: flex;
    height: 100%;
    position: absolute;
    margin: auto;
    left: 0;
    right: 0;
    width: 1px;
    background-color: #d4dde2;
  }
}

.modal-default-button {
  float: right;
}

/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
</style>
