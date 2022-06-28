<template>
    <div class="card">
        <div class="card__header">
            <h2 class="card__user">Пользователь {{ item.userId }}</h2>
            <div class="card__count" :class="`card__count_${setStatus}`">
                <span>{{ item.completed }}/</span><span>{{ item.totalCompleted }}</span>
            </div>
        </div>

        <ul class="card__titles">
            <li class="card__title" v-for="(itemTitle,idx) in item.titles" :key="idx">
                {{ itemTitle }}
            </li>
        </ul>
    </div>
</template>

<script>
export default {
    name: 'AppCard',
    props: ['item'],
    computed: {
        setStatus(){
            if (this.item.completed === this.item.totalCompleted){
                return 'ok'
            }
            if(this.item.completed < this.item.totalCompleted){
                return 'process'
            }
            return 'error'
        }
    }
}
</script>

<style scoped>
.card {
    min-width: 170px;
    max-width: 200px;
    height: 200px;
    display: flex;
    flex-direction: column;
    margin-bottom: 10px;
    margin-right: 10px;
    padding: 20px;
    border-radius: 12px;
    background-color: #87c9a7;
}

.card__header {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    align-items: baseline;
    font-size: 14px;
    margin-bottom: 10px;


}

.card__user {
    font-size: 14px;
}

.card__titles {
    display: flex;
    flex-direction: column;
    justify-content: left;
    white-space: pre-wrap;
    overflow: hidden;
    overflow-y: scroll;
    padding:0 10%;

}

.card__titles::-webkit-scrollbar {
    width: 6px;
    border-radius: 4px;
    background: #F8F8F8;
}

.card__titles::-webkit-scrollbar-thumb {
    width: 6px;
    border-radius: 4px;
    background: #E4E4E4;
}

.card__title {
    font-size: 11px;
    list-style-type: circle;
    text-align: left;
}
.card__count {
    font-size: 14px;
    font-weight: bold;
    padding: 4px;
    border-radius: 8px;
}
.card__count_ok{
    background-color: mediumseagreen;
}
.card__count_process{
    background-color:#e5f64f
}
.card__count_error{
    background-color:#f84d4db3
}

</style>