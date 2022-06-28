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
.button {
    padding: 1rem;
    border: 0px;
    border-radius: 10px;
    background-color: mediumseagreen;
    font-weight: 500;
    margin-bottom: 10px;
    color: white;

}

button:hover {
    cursor: pointer;
    font-weight: 600;
    outline: 1px solid greenyellow;
}
</style>