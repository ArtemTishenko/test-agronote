<template>
    <div class="wrapper">
        <button class="button" @click="getData" :disabled="isLoading">Загрузить данные</button>
       <p>        {{plotDataAll}}
           {{plotDataCompleted}} {{plotDataLabels}}</p>
        <div class="loader" v-if="isLoading">
            <img class="icon-load" src="./assets/spinner.svg" alt="spinner">
        </div>
        <div v-else class="wrapper wrapper__cards">
            <ul class="cards">
                <li v-for="(item) in sortingCards" :key="item.userId">
                    <AppCard :item="item"></AppCard>
                </li>
            </ul>
        </div>
        <div class="wrapper__chart">
            <BarChart ref="bar"
                :chart-data="chartData"
            />
        </div>

    </div>

</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
// import Cards from './components/Cards.vue'
// import AppButton from "@/components/AppButton";
import axios from "axios";
import AppCard from "@/components/AppCard";
import BarChart from "@/components/BarChart";

export default {
    name: 'App',
    components: {
        // HelloWorld,
        // Cards,
        // AppButton,
        AppCard,
        BarChart
    },
    data() {
        return {
            responseData: {},
            isLoading:false,
            chartData: {
                labels: [ 'User1', 'User2', 'User3' ],
                datasets: [
                    {
                        label:'all',
                        data: [40, 20, 12],
                        backgroundColor: '#f87979',
                        order:2
                    },
                    {
                        label:'done',
                        data: [30, 10, 2],
                        backgroundColor: '#1E90FF',
                        order:1
                    }
                ]
            },
        }
    },
    computed: {
        sortingCards() {
            return this.arrCards.sort(function (a, b) {
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
        plotDataAll(){
            let arr = []
            this.arrCards.forEach((item)=>{
                arr.push(item.completed)
            })
            return arr
        },
        plotDataCompleted(){
          let arr = []
            this.arrCards.forEach((item)=>{
                arr.push(item.totalCompleted)
            })
          return arr
        },
        plotDataLabels(){
            let arr=[]
            this.arrCards.forEach((item)=>{

            })

            return arr
        },
        arrCards() {
            let arrResult = []
            let result = {}
            let resultData = JSON.parse(JSON.stringify(this.responseData))

            let arrCards = []
            let accumCards = {
                userId: 0,
                titles: [],
                totalCompleted: 0,
                completed: 0,
            }
            if (!(resultData && Object.keys(resultData).length === 0 && Object.getPrototypeOf(resultData) === Object.prototype)) {
                for (let i = 0; i < resultData.length; i++) {
                    if ((i + 1) < resultData.length) {
                        if (resultData[i].userId === resultData[i + 1].userId) {
                            accumCards.userId = resultData[i].userId
                            accumCards.titles.push(resultData[i].title)
                            accumCards.totalCompleted = accumCards.totalCompleted + 1
                            if (resultData[i].completed) {
                                accumCards.completed = accumCards.completed + 1
                            }
                        } else {
                            accumCards.totalCompleted = accumCards.totalCompleted + 1
                            if (resultData[i].completed) {
                                accumCards.completed = accumCards.completed + 1
                            }

                            let cardItem = JSON.stringify(accumCards)
                            arrCards.push(JSON.parse(cardItem))

                            accumCards.userId = 0
                            accumCards.titles = []
                            accumCards.totalCompleted = 0
                            accumCards.completed = 0
                        }
                    } else {
                        if (resultData[i - 1].userId === resultData[i].userId) {
                            accumCards.titles.push(resultData[i].title)
                            accumCards.totalCompleted = accumCards.totalCompleted + 1
                            if (resultData[i].completed) {
                                accumCards.completed = accumCards.completed + 1
                            }
                            let cardItem = JSON.stringify(accumCards)
                            arrCards.push(JSON.parse(cardItem))
                        }
                    }
                }
            }
            return arrCards
        }
    },
    methods: {
        getData() {
            this.isLoading = true
            axios({
                method: 'get',
                url: 'https://jsonplaceholder.typicode.com/todos',
            }).then((response) => {
                this.isLoading = false
                let resArr = (JSON.parse(JSON.stringify(response))).data
                this.responseData = [...resArr]
            })
                .catch((err) => {
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
    margin-top: 60px;

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

}

.wrapper__cards {
    padding: 1rem;
}

.cards {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    flex-wrap: wrap;
}
.button {
    padding: 1rem;
    border: 0px;
    border-radius: 10px;
    background-color: mediumseagreen;
    font-weight: 500;
    margin-bottom: 10px;
    color: white;

}

.button:disabled{
    background-color: gray;
}
.button:hover {
    cursor: pointer;
    font-weight: 600;
    outline: 1px solid greenyellow;
}
.wrapper__chart {
    width: 500px;
}
</style>
