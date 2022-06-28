<template>
    <img alt="Vue logo" src="./assets/logo.png">
    <div class="wrapper">
        <button class="button" @click="getData">Загрузить данные</button>
        <div class="wrapper wrapper__cards">
            <p>accountCompleted:{{ accountOfCompleted }}</p>
            <p></p>
            <ul class="cards">
                <li v-for="(item) in cards" :key="item.id">
                    <AppCard :item="item"></AppCard>
                </li>
            </ul>
        </div>
    </div>

</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
// import Cards from './components/Cards.vue'
// import AppButton from "@/components/AppButton";
import axios from "axios";
import AppCard from "@/components/AppCard";

export default {
    name: 'App',
    components: {
        // HelloWorld,
        // Cards,
        // AppButton,
        AppCard
    },
    data() {
        return {
            cards:[
                // {
                //     userId:1,
                //     titles:["delectus aut autem", ],
                //     totalCompleted: 10,
                //     completed: 5,
                //
                // },
                // {
                //     userId:2,
                //     titles:["delectus aut autem", ],
                //     totalCompleted: 13,
                //     completed: 9
                // }
            ],
            responseData: {},
            example:{
                completed: false,
                id: 1,
                title: "delectus aut autem",
                userId: 1
            }
        }
    },
    computed:{
        accountOfCompleted(){
            let arrResult = []
            let result = {}
            let resultData = JSON.parse(JSON.stringify(this.responseData))

            let arrCards = []
            let accumCards = {
                userId:0,
                titles:[],
                totalCompleted: 0,
                completed: 0,
            }
            // result = resultData
            // console.log('!!!!!',resultData)
            debugger
            if (!(resultData && Object.keys(resultData).length === 0 && Object.getPrototypeOf(resultData) === Object.prototype)){
                console.log('!!!',resultData)

                for(let i = 0; i < resultData.length; i++){
                    if(((i+1)<resultData.length)){
                        console.log('11111111',resultData[i].userId, resultData[i+1].userId , accumCards)
                        if (resultData[i].userId === resultData[i+1].userId){
                            accumCards.userId = resultData[i].userId
                            accumCards.titles.push(resultData[i].title)
                            accumCards.totalCompleted = accumCards.totalCompleted+1
                            if( resultData[i].completed){
                                accumCards.completed = accumCards.completed +1
                            }
                        }else{
                            let test = JSON.stringify(accumCards)
                            arrCards.push(JSON.parse(test))
                            accumCards.userId = 0
                            accumCards.titles = []
                            accumCards.totalCompleted = 0
                            accumCards.completed = 0
                        }
                    }


                }
                console.log('arrCards=',arrCards)
            }

            return arrCards
        }
    },
    methods: {
        addToCardsArray(item){
            console.log('payload',item)
            this.cards.push(item)
        },
        getData() {
            let res = null
            axios({
                method: 'get',
                url: 'https://jsonplaceholder.typicode.com/todos',
            }).then((response) => {
                let resArr = (JSON.parse(JSON.stringify(response))).data
                this.responseData = [...resArr]
                // this.responseData.forEach((item)=>{
                //     item = JSON.parse(JSON.stringify(item))
                //     // return JSON.parse(JSON.stringify(item))
                // })

                // console.log(this.responseData)
                //  res = this.responseData.reduce((acc,item)=>{
                //
                //      console.log(JSON.parse(JSON.stringify(item)))
                //      ++acc
                //      return acc
                //
                // },0)
                // console.log(res)
                // return JSON.parse(JSON.stringify(res))

            })
            .catch((err) => {
                console.log(err)
                return res
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

</style>
