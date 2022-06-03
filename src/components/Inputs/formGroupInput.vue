<template>
  <div
    class="form-group col-11 mx-auto"
    :class="[
      { 'input-group': hasIcon },
      { 'has-danger': error },
      { 'input-group-focus': focused },
      { 'has-label': label || $slots.label },
      { 'row': inline }
    ]"
  >
    <slot name="label">
      <label v-if="label" class="control-label col-12 my-auto" :class="[{ 'col-md-3 col-lg-3': inline }, labelClasses]">
        {{ label }}
        <span class="text-danger" v-if="required">*</span>
      </label>
    </slot>

    <div class="col-12 my-auto px-lg-0 px-md-0" :class="[{ 'input-group': hasIcon }, { 'col-md-9 col-lg-9': inline }, inputGroupClasses]">
      <slot name="addonLeft">
        <div v-if="addonLeftIcon" class="input-group-prepend">
          <i class="input-group-text" :class="addonLeftIcon"></i>
        </div>
      </slot>
      <slot>
        <input
          :value="value"
          v-on="listeners"
          v-bind="$attrs"
          class="form-control"
          :class="[{ valid: value && !error }, inputClasses]"
          aria-describedby="addon-right addon-left"
        />
      </slot>
      <slot name="addonRight">
        <span
          v-if="addonRightIcon"
          class="input-group-addon input-group-append"
        >
          <i class="input-group-text" :class="addonRightIcon"></i>
        </span>
      </slot>

      <slot name="infoBlock"></slot>
      <slot name="helpBlock">
        <div
          class="text-danger invalid-feedback"
          style="display: block;"
          :class="{ 'mt-2': hasIcon }"
          v-if="error"
        >
          {{ error }}
        </div>
      </slot>
    </div>
  </div>
</template>
<script>
export default {
  inheritAttrs: false,
  name: 'fg-input',
  props: {
    required: Boolean,
    inline: Boolean,
    label: String,
    error: String,
    labelClasses: String,
    inputClasses: String,
    inputGroupClasses: String,
    value: {
      type: [String, Number],
      default: ''
    },
    addonRightIcon: String,
    addonLeftIcon: String
  },
  data() {
    return {
      focused: false
    };
  },
  computed: {
    listeners() {
      return {
        ...this.$listeners,
        input: this.updateValue,
        focus: this.onFocus,
        blur: this.onBlur
      };
    },
    hasIcon() {
      const { addonRight, addonLeft } = this.$slots;
      return (
        addonRight !== undefined ||
        addonLeft !== undefined ||
        this.addonRightIcon !== undefined ||
        this.addonLeftIcon !== undefined
      );
    }
  },
  methods: {
    updateValue(evt) {
      let value = evt.target.value;
      this.$emit('input', value);
    },
    onFocus(value) {
      this.focused = true;
      this.$emit('focus', value);
    },
    onBlur(value) {
      this.focused = false;
      this.$emit('blur', value);
    }
  }
};
</script>
<style></style>
