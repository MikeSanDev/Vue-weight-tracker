<script setup>
import {ref, shallowRef, computed, watch, nextTick} from "vue";
import Chart from 'chart.js/auto';

const weights = ref([])

const weightChartEl = ref(null)

const weightChart = shallowRef(null)

const weightInput = ref(180.00)

const currentWeight = computed(() => {
    return weights.value.sort((a, b) => b.date - a.date)[0] || { weight: 0}
})

const addWeight = () => {
    weights.value.push({
        weight: weightInput.value,
        date: new Date().getTime()
    })
}

watch(weights, newWeights => {
    const ws = [...newWeights]

    if (weightChart.value) {
        weightChart.value.data.labels = ws
        .sort((a, b) => a.date - b.date)
        .map(w => new Date(w.date).toLocaleDateString())
        .slice(-7)
    }

    nextTick(() => {
        weightChart.value = new Chart(weightChartEl.value.getContext('2d'), {
            type: 'line',
            data: {
                labels: ws
                .sort((a, b) => a.date - b.date)
                .map(w => new Date(w.date).toLocaleDateString()),
                datasets:  [
                    {
                        label: 'weight',
                        data: ws
                            .sort((a, b) => a.date - b.date)
                            .map(w => w.weight),
                            backgroundColor: 'rgba(255, 105. 180, 0.2)',
                            borderColor: 'rgba(255, 105. 180)',
                            borderWidth: 1,
                            fill: true
                    }
                ]
            },
            options: {
                responsive: true,
                maintainAspectRations: false
            }
        })
    })

        console.log(ws)
    }, {deep: true})

</script>

<template>
    <main>
        <h1> Weight Tracker </h1>

        <div>
            <span>{{  currentWeight.weight }}</span>
            <small>Current Weight (lbs)</small>
        </div>

        <form @submit.prevent="addWeight">

            <input type="number" step="0.1" v-model="weightInput"/>
            <input type="submit" value="Add Weight"/>
        </form>
        <div v-if="weights && weights.length > 0">
            <h2>Last 7 Days</h2>

            <div class="canvas-box">
                <canvas ref='weightChartEl'></canvas>
            </div>

            <div>
                <h2>Weight History</h2>
                    <ul>
                        <li v-for="weight in weights">
                            <span>
                                {{  weight.weight }}lbs
                            </span>
                            <small>
                                {{  new Date(weight.date).toLocaleDateString() }}
                            </small>
                        </li>    
                    </ul>
            </div>
        </div>
    </main>
</template>

<style>

</style>
