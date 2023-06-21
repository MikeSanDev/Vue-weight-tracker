<script setup>
import {ref, shallowRef, computed, watch, nextTick} from "vue";
import Chart from 'chart.js/auto';

const weights = ref({})

const weightChartEl = ref(null)

const weightChart = shallowRef(null)

const weightInput = ref(180.00)


</script>

<template>
<h1>Hello World!</h1>
</template>

<style>

</style>
