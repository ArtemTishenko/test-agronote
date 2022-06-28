<template>
    <div>

    </div>
</template>

<script>
import axios from 'axios'

export default {
    name: 'AppButton',
    data() {
        return {
            responseData:null
        }
    },
    methods: {
        getData() {
            axios({
                method: 'get',
                url: 'https://jsonplaceholder.typicode.com/todos',
            }).then((response)=>{
                let test = JSON.parse(JSON.stringify(response)).data
                console.log(test)
                // this.responseData = [...test]
                this.responseData =  [...Object.assign(response).data]
                console.log( this.responseData)
            }).catch((err)=>{
                console.log('ERROR',err)
            })
        }
    }
}
</script>

<style scoped>

</style>