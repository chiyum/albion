<template>
  <div class="wave-group">
    <input
      required=""
      type="text"
      class="input"
      v-model="value"
      @input="numberReplace"
    />
    <span class="bar"></span>
    <label class="label">
      <span
        class="label-char"
        v-for="(str, index) in label"
        :key="str + index"
        :style="`--index: ${index}`"
        >{{ str }}</span
      >
      <!-- <span class="label-char" style="--index: 1">a</span>
      <span class="label-char" style="--index: 2">m</span>
      <span class="label-char" style="--index: 3">e</span> -->
    </label>
  </div>
</template>
<script setup>
import { defineProps, computed } from "vue";
const props = defineProps({
  propsLabel: String,
  modelValue: String | Number,
  inputType: { type: String, default: "text" },
});
const emit = defineEmits(["update:modelValue"]);
const label = computed(() => {
  const strings = props.propsLabel.split("");
  return strings;
});
const value = computed({
  get() {
    return props.modelValue;
  },
  set(value) {
    // const updateValue =
    //   props.inputType === "text" ? value : numberReplace(value);
    emit("update:modelValue", value);
  },
});

const numberReplace = (el) => {
  if (props.inputType !== "number") return;
  /* 替换掉所有非数字字符 **/
  const res = el.target.value.replace(/\D/g, "");
  el.target.value = res;
  // return `${res}`;
};
</script>

<style>
.wave-group {
  position: relative;
  margin-top: 0.5rem;
}

.wave-group .input {
  font-size: 16px;
  padding: 10px 10px 10px 5px;
  display: block;
  width: 150px;
  border: none;
  border-bottom: 1px solid #515151;
  background: transparent;
}

.wave-group .input:focus {
  outline: none;
}

.wave-group .label {
  color: #999;
  font-size: 18px;
  font-weight: normal;
  position: absolute;
  pointer-events: none;
  left: 5px;
  top: 10px;
  display: flex;
}

.wave-group .label-char {
  transition: 0.2s ease all;
  transition-delay: calc(var(--index) * 0.05s);
}

.wave-group .input:focus ~ label .label-char,
.wave-group .input:valid ~ label .label-char {
  transform: translateY(-20px);
  font-size: 14px;
  color: #5264ae;
}

.wave-group .bar {
  position: relative;
  display: block;
  width: 150px;
}

.wave-group .bar:before,
.wave-group .bar:after {
  content: "";
  height: 2px;
  width: 0;
  bottom: 1px;
  position: absolute;
  background: #5264ae;
  transition: 0.2s ease all;
  -moz-transition: 0.2s ease all;
  -webkit-transition: 0.2s ease all;
}

.wave-group .bar:before {
  left: 50%;
}

.wave-group .bar:after {
  right: 50%;
}

.wave-group .input:focus ~ .bar:before,
.wave-group .input:focus ~ .bar:after {
  width: 50%;
}
</style>
