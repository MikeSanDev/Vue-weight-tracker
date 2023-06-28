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

        weightChart.value.data.datasets[0].data = ws
        .sort((a, b) => a.date - b.date)
        .map(w => w.weight)
        .slice(-7)

        weightChart.value.update()

        return
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
                        label: 'Weight',
                        data: ws
                            .sort((a, b) => a.date - b.date)
                            .map(w => w.weight),
                            backgroundColor: '#f6d9b1',
                            borderColor: '#fd9591 ',
                            borderWidth: 2,
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

        <div class="current">
            <small>Current Weight (lbs): </small>
            <span>{{  currentWeight.weight }}</span>
        </div>

        <form @submit.prevent="addWeight">

            <input type="number" step="0.1" v-model="weightInput"/>
            <input type="submit" value="Add Weight"/>
        </form>
        <div class="history-box" v-if="weights && weights.length > 0">
            <h2>Last 7 Days</h2>

            <div class="canvas-box">
                <canvas ref='weightChartEl'></canvas>
            </div>

            <div class="history-div">
                <h2>Weight History</h2>
                <table>
        <thead>
            <tr>
            <th>Weight (lbs)</th>
            <th>Date</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="weight in weights" :key="weight.date">
            <td>{{ weight.weight }}lbs</td>
            <td>{{ new Date(weight.date).toLocaleDateString() }}</td>
            </tr>
        </tbody>
    </table>
            </div>
        </div>
    </main>
</template>

<style>
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
	font-family: 'montserrat', sans-serif;
}

body {
	background-color: #eee;
}

main {
	padding: 1.5rem;
}

h1 {
	font-size: 2em;
	text-align: center;
	margin-bottom: 2rem;
    
}

h2 {
	margin-bottom: 1rem;
	color: #888;
	font-weight: 400;
}

.current {
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;

	width: 200px;
	height: 200px;
	
	text-align: center;
	background-color: white;
	border-radius: 999px;
	box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
	border: 5px solid #f6d9b1;
	
	margin: 0 auto 2rem;
}

.current span {
	display: block;
	font-size: 2em;
	font-weight: bold;
	margin-bottom: 0.5rem;
}

.current small {
	color: #888;
	font-style: italic;
}

form {
	display: flex;
	margin-bottom: 2rem;
	border: 1px solid #AAA;
	border-radius: 0.5rem;
	overflow: hidden;
	transition: 200ms linear;
}

form:focus-within,
form:hover {
	border-color: #f6d9b1;
	border-width: 2px;
}

form input[type="number"] {
	appearance: none;
	outline: none;
	border: none;
	background-color: white;

	flex: 1 1 0%;
	padding: 1rem 1.5rem;
	font-size: 1.25rem;
}

form input[type="submit"] {
	appearance: none;
	outline: none;
	border: none;
	cursor: pointer;
	background-color: #fd9591;

	padding: 0.5rem 1rem;

	color: white;
	font-size: 1.25rem;
	font-weight: 700;
	transition: 200ms linear;
	border-left: 3px solid transparent;
}

form input[type="submit"]:hover {
	background-color: white;
	color: #fd9591;
	border-left-color: #f6d9b1;
}

.history-box{
    display: flex;
    align-items: center;
    flex-direction: column;
}
.canvas-box {
	width: 100%;
	max-width: 720px;
	background-color: white;
	padding: 1rem;
	border-radius: 0.5rem;
	box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
	margin-bottom: 2rem;
}

.history-date {
    font-size: 17px;
}
.history-div{

    text-align: center;
}
table {
  border-collapse: collapse;
  width: 100%;
}

th,
td {
  padding: 8px;
  border: 1px solid black;
}
</style>
