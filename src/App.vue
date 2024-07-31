<template>
  <main>
    <div class="input">
      <input ref="colorHEX" v-model="color.value" type="text" placeholder="Color HEX" @input="validateInput"/>
      <input type="color" v-model="color.value"/>
    </div>
    <div class="main-color">{{ color.name }}</div>

    <code class="result">
      <span class="line">
        <b>background: </b>
        <i>{{ color.hex }};</i>
      </span>
      <span class="line">
        <b>background: </b>
        <i>{{ hexToRgb(color.hex) }};</i>
      </span>
      <span class="line">
        <b>color: </b>
        <i>{{ color.hex }};</i>
      </span>
      <span class="line">
        <b>${{ color.name }}: </b>
        <i>{{ color.hex }};</i>
      </span>
      <span class="line">
        <b>.c-{{ color.name }} {<br></b>
        <i>&nbsp;&nbsp;color: ${{ color.name }};<br>}</i>
      </span>
      <span class="line">
        <b>.bg-{{ color.name }} {<br></b>
        <i>&nbsp;&nbsp;background-color: ${{ color.name }};<br>}</i>
      </span>
    </code>
  </main>

  <h1 class="color-name" :style="{textTransform}">
    {{ color.name }}
  </h1>

  <div class="about">
    Based on <a href="https://chir.ag/projects/ntc/" target="_blank">NTC</a><br>
    Created by <a href="https://dan13.me/?utm_source=colorname">DanRotaru</a>
  </div>
</template>

<script setup>
import {onMounted, reactive, ref, watch, computed} from "vue";
import ntc from "@/assets/ntc";

const colorHEX = ref(null);
const color = reactive({
  name: 'white',
  hex: '#FFFFFF',
  value: null
});
const textTransform = computed(() => (/[A-Z]/).test(color.name) ? 'none' : 'uppercase');

onMounted(() => colorHEX.value.focus());

watch(() => color.value, value => {
  if (!value.toString().startsWith('#')) {
    value = '#' + value;
  }

  const ntcResult = ntc.name(value);

  console.log(ntcResult);

  if (ntcResult) {
    color.hex = value;
    color.name = ntcResult[2];
    setColor(color.hex);
  }
});

const hexPattern = /^#([A-Fa-f0-9]{6}|[A-Fa-f0-9]{3})$/;
const validateInput = (event) => {
  const value = event.target.value;
  if (value.length > 7) {
    color.value = value.slice(0, 7);
  }
};

const rootStyles = document.querySelector(':root');
const darkBgColors = ['#000', '#000000', '#111', '#111111'];

const setColor = (value) => {
  rootStyles.style.setProperty('--color', value);
  rootStyles.style.setProperty('--text-color', darkBgColors.includes(value) ? '#fff' : value);
}

function hexToRgb(hex) {
  const result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
  return result
    ? `rgb(${parseInt(result[1], 16)} ${parseInt(result[2], 16)} ${parseInt(result[3], 16)})`
    : null;
}

</script>
