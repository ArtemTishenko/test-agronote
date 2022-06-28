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
            // result = resultData
            // console.log('!!!!!',resultData)

            if (!(resultData && Object.keys(resultData).length === 0 && Object.getPrototypeOf(resultData) === Object.prototype)){
                console.log('!!!',resultData)
                result = resultData.reduce((acc,item)=>{
                    const objItem = JSON.parse(JSON.stringify(item))
                    // console.log('objItem.userId',objItem.userId,resultData[idx].userId)
                    if(objItem.userId === 1){
                        acc.titles.push(objItem.title)
                        acc.userId = objItem.userId
                        acc.totalCount = acc.totalCount +1
                        if(objItem.completed){
                            acc.count = acc.count+1
                            this.addToCardsArray(acc)
                        }
                    }
                    return acc
                },{
                    userId:0,
                    totalCount:0,
                    count:0,
                    titles:[]
                })
            }

            console.log('arrResult',arrResult)
            return result
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
