<template>
    <div class="card">
        <h2 class="card__user">Пользователь {{ item.userId }}</h2>
        <div class="card__count">
            <span>{{ item.count }}/</span><span>{{ item.totalCount }}</span>
        </div>
        <p>№{{ item.id }}</p>
        <ul class="card__titles">
            <li class="card__title" v-for="(itemTitle,idx) in item.titles" :key="idx">
                <p>{{itemTitle}}</p>
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
        padding: 5px;
        overflow: hidden;
        overflow-y: scroll;

    }
    .card__user{
        font-size: 12px;
    }
    .card__titles{
        list-style-type: circle;

    }
    .card__title{
        font-size: 11px;
    }
</style>