<template>
  <div
    :class="[
      'select-option',
      { 'is-selected': isSelect },
      { 'is-disabled': disabled }
    ]"
    @click.stop="handleClick"
  >
    <slot></slot>
  </div>
</template>

<script>
export default {
  props: {
    value: { type: [Object, String, Number], required: true },
    label: { type: String },
    disabled: { type: Boolean, defa: false }
  },
  inject: {
    fatSelect: { default: "fatSelect" }
  },
  computed: {
    isSelect() {
      const {
        fatSelect: { optionKey, selectItems }
      } = this;
      const key = this[optionKey] || this.$attrs[optionKey];

      return selectItems.find(item => item.key === key);
    }
  },
  watch: {
    ["fatSelect.selectValue"]: {
      handler(newValue) {
        const {
          value,
          label,
          fatSelect: { optionKey, multiple, selectValue }
        } = this;
        const key = this[optionKey] || this.$attrs[optionKey];

        if (
          newValue === value ||
          (Array.isArray(newValue) && newValue.find(item => item === value))
        ) {
          if (!multiple) {
            this.fatSelect.selectItems = [
              {
                key,
                label,
                value
              }
            ];
          } else {
            this.fatSelect.selectItems.push({
              key,
              label,
              value
            });
          }
        }
      },
      immediate: true
    }
  },
  methods: {
    handleSelect(key) {
      let {
        fatSelect: { multiple, selectValue },
        value,
        label,
        isSelect
      } = this;

      if (!multiple) {
        this.fatSelect.selectValue = value;
        this.fatSelect.isOpen = false;
      } else {
        if (isSelect) {
          this.fatSelect.selectValue = selectValue.filter(
            item => item !== value
          );
        } else {
          this.fatSelect.selectValue.push(value);
        }
      }
      this.fatSelect.$emit("change", this.fatSelect.selectValue);
      this.fatSelect.$emit("input", this.fatSelect.selectValue);
    },

    handleClick() {
      let {
        fatSelect: { multiple, optionKey, selectValue },
        disabled
      } = this;
      const key = this[optionKey] || this.$attrs[optionKey];

      if (!disabled) {
        this.handleSelect(key);
      }
    }
  }
};
</script>

<style lang="scss">
$success-color: #209cee;
$disabled-color: #c0c4cc;
.select-option {
  padding: 0 10px;
  cursor: pointer;

  // &:not(.is-disabled):hover {
  //   background-color: #F2F2F2;
  // }
  &:hover{
    background-color: #F2F2F2;
  }

  &.is-selected {
    background-color: #006eff;
    color: #fff;
    &:hover{
         background-color: #006eff;
    color: #fff; 
    }
  }

  &.is-disabled {
    // color: $disabled-color;
        background-color: #F2F2F2;
    cursor: not-allowed;
  }
}
</style>
