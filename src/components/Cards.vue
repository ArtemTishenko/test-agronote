<template>
    <div class="wrapper wrapper__cards">
        <ul class="cards">
            <li v-for="(item,idx) in 5" :key="idx">
                <card :item="item"></card>
            </li>
        </ul>
    </div>

</template>

<script>
import Card from "@/components/AppCard";

export default {

    name: 'AppCards',
    props: {
        msg: String
    },
    components: {
        Card
    },

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.cards {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
}
</style>
