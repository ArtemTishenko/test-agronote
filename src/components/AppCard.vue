<template>
    <div class="card">
        <h2 class="card__user">Пользователь {{ item.userId }}</h2>
        <div class="card__count">
            <span>{{ item.completed }}/</span><span>{{ item.totalCompleted }}</span>
        </div>
        <ul class="card__titles">
            <li class="card__title" v-for="(itemTitle,idx) in item.titles" :key="idx">
                {{itemTitle}}
            </li>
        </ul>
    </div>
</template>

<script>
export default {
    name:'AppCard',
    props:['item'],
    computed:{
       // totalCompleted(){
       //     let result = this.item
       //     return result
       // }
    }
}
</script>

<style scoped>
    .card{
        width: 200px;
        background-color: lightgray;
        height: 100px;
        display: flex;
        flex-direction: column;
        margin-bottom: 10px;
        border-radius: 12px;
        padding: 20px;
        overflow: hidden;
        overflow-y: scroll;

    }
    .card::-webkit-scrollbar {
         width: 6px;
         border-radius: 4px;
         background: #F8F8F8;
     }
    .card::-webkit-scrollbar-thumb {
         width: 6px;
         border-radius: 4px;
         background: #E4E4E4;
     }
    .card__user{
        font-size: 12px;
    }
    .card__titles{

        display: flex;
        flex-direction: column;
        justify-content: left;

    }
    .card__title{
        font-size: 11px;
        list-style-type: circle;
        text-align: left;
    }
</style>