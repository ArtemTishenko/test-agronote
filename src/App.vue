<template>
    <div class="wrapper">
        <section class="wrapper__button">
            <button class="button" @click="handlerClickButton" :disabled="isLoading">Загрузить данные</button>
            <div class="loader" v-if="isLoading">
                <img class="loader__icon" src="./assets/spinner.svg" alt="spinner">
            </div>
        </section>
                <!--        <section class="wrapper wrapper__cards">-->
                <!--            <ul class="cards">-->
                <!--                <li v-for="(item) in sortingCards" :key="item.userId">-->
                <!--                    <AppCard :item="item"></AppCard>-->
                <!--                </li>-->
                <!--            </ul>-->
                <!--        </section>-->
                <!--        <section class="wrapper__chart">-->
                <!--            <BarChart ref="bar"-->
                <!--                      :chart-data="plotChartData"-->
                <!--                      :isLoadedData="isLoading"-->
                <!--            />-->
                <!--        </section>-->
        <section>
            <app-map></app-map>
        </section>

    </div>

</template>

<script>

import axios from "axios";
import AppCard from "@/components/AppCard";
import BarChart from "@/components/BarChart";
import AppMap from "@/components/AppMap";

export default {
    name: 'App',
    components: {
        AppMap,
        AppCard,
        BarChart
    },
    data() {
        return {
            responseData: {},
            isLoading: false,
            chartData: {},
        }
    },
    computed: {
        sortingCards() {
            let sortedCarts = [...this.arrCards]

            return sortedCarts.sort((a, b) => {
                if (a.completed - b.completed < 0) {
                    return 1
                } else if (a.completed - b.completed > 0) {
                    return -1
                } else {
                    if (a.userId - b.userId < 0) {
                        return -1
                    } else {
                        return 1
                    }
                }
            })
        },
        plotData() {
            let plotData = {
                all: [],
                completed: [],
                labels: []
            }

            this.arrCards.forEach((item) => {
                plotData.all.push(item.totalCompleted)
                plotData.completed.push(item.completed)
                plotData.labels.push(`Пользовтель ${item.userId}`)
            })
            return plotData
        },
        plotChartData() {
            return {
                labels: this.plotData.labels,
                datasets: [
                    {
                        label: 'All',
                        data: this.plotData.all,
                        backgroundColor: '#f87979',
                        order: 2
                    },
                    {
                        label: 'Completed',
                        data: this.plotData.completed,
                        backgroundColor: '#1E90FF',
                        order: 1
                    }
                ]
            }
        },
        arrCards() {
            let resultData = JSON.parse(JSON.stringify(this.responseData))
            let arrCards = []
            let accumCard = {
                userId: 0,
                titles: [],
                totalCompleted: 0,
                completed: 0,
            }
            if (!(resultData && Object.keys(resultData).length === 0 && Object.getPrototypeOf(resultData) === Object.prototype)) {

                for (let i = 0; i < resultData.length; i++) {
                    if ((i + 1) < resultData.length) {
                        if (resultData[i].userId === resultData[i + 1].userId) {
                            accumCard.userId = resultData[i].userId
                            accumCard.titles.push(resultData[i].title)
                            accumCard.totalCompleted++
                            if (resultData[i].completed) {
                                accumCard.completed++
                            }
                        } else {
                            accumCard.totalCompleted++
                            if (resultData[i].completed) {
                                accumCard.completed++
                            }
                            arrCards.push(JSON.parse(JSON.stringify(accumCard)))

                            accumCard.userId = 0
                            accumCard.titles = []
                            accumCard.totalCompleted = 0
                            accumCard.completed = 0
                        }
                    } else {
                        if (resultData[i - 1].userId === resultData[i].userId) {
                            accumCard.titles.push(resultData[i].title)
                            accumCard.totalCompleted++
                            if (resultData[i].completed) {
                                accumCard.completed++
                            }
                            arrCards.push(JSON.parse(JSON.stringify(accumCard)))
                        }
                    }
                }
            }

            return arrCards
        }
    },
    methods: {
        async handlerClickButton() {
            await this.getData()
        },
        getData() {
            this.isLoading = true

            return axios({
                method: 'get',
                url: 'https://jsonplaceholder.typicode.com/todos',
            }).then((response) => {
                this.isLoading = false
                let resArr = (JSON.parse(JSON.stringify(response))).data
                this.responseData = [...resArr]
            }).catch((err) => {
                this.isLoading = false
                console.log(err)
            })
        }
    }
}

</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 1rem;

}

ul {
    margin: 0;
    padding: 0;
    list-style-type: none;

}

h2 {
    margin: 0;
    padding: 0;
}

.wrapper {
    padding: 1rem;
}

.wrapper__cards {
    padding: 1rem;
}

.wrapper__button {
    display: flex;
    flex-direction: row;
    align-items: center;
}

.cards {
    display: flex;
    flex-direction: row;
    justify-content: start;
    flex-wrap: wrap;
}

.loader {
    width: 20px;
    height: 20px;
    transition: all 0.3s ease;
}

.loader__icon {
    width: 100%;
    height: 100%;
}

.button {
    padding: 1rem;
    border: 0;
    border-radius: 10px;
    background-color: mediumseagreen;
    font-weight: 500;
    color: white;
    margin-right: 20px;
    transition: all 0.3s ease;
}

.button:disabled {
    background-color: gray;
}

.button:disabled:hover {
    opacity: 1;
    cursor: not-allowed;
}

.button:hover {
    cursor: pointer;
    opacity: 0.8;
}

.wrapper__chart {
    display: flex;
    justify-content: center;
}
</style>
