<template>
    <div class="wrapper">
        <div class="wrapper__app-map">
            <section class="wrapper__menu" :class="openMenu ? 'wrapper__menu_open': ''">
                <ul v-if="openMenu" class="menu">
                    <li class="menu__item" @click="handlerMenuItemClick">Осмотр</li>
                </ul>
                <div class="menu__footer">
                    <button class="menu__button"
                            @click="openMenu = !openMenu">
                        <span class="menu__button-icon">
                            <img class="menu__icon-image" src="./assets/burger.svg" alt="burger-menu">
                        </span>

                    </button>
                </div>
            </section>
            <section class="wrapper__map" >
                <app-map :openMenu="openMenu" ref="appMap"></app-map>
            </section>
        </div>
    </div>

</template>

<script>

import AppMap from "@/components/AppMap";

export default {
    name: 'App',
    components: {
        AppMap,

    },
    data() {
        return {
           openMenu:false
        }
    },
    computed: {

    },
    methods: {
        handlerMenuItemClick(){

            this.$refs.appMap.handlerBtnInspection()

        }
    }
}

</script>

<style>
body {
    margin: 0;
}
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
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


.wrapper{
    padding: 0;
    margin: 0;
}
.wrapper__app-map{
    display: flex;
    flex-direction: row;
    height: 100vh;
    align-items: stretch;
}
.wrapper__map{
    /*position: relative;*/
    margin-left: 50px;
    transition: 0.3s;
}
.wrapper__menu {
    width: 50px;
    background: rgba(17, 16, 16, 0.6);
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    transition: all 0.3s ease;
    height: 100%;
    top: 0;
    left: 0;
    position: fixed;
    overflow-x: hidden;

}
.wrapper__menu_open{
    width: 250px;
}
.menu{

}
.menu__item{
    padding: 20px;
    opacity: 1;
    font-size: 16px;
    font-weight: 600;
    transition: all ease 0.3s;
    cursor: pointer;
}
.menu__item:hover{
    background: white;
    opacity: 0.8;


}
.menu__button{
    background: none;
    border: none;
    font-weight: 700;
    font-size: 21px;
    cursor: pointer;
    padding: 0;
}
.menu__button:hover{
    color: white
}
.menu__button-icon{
    width: 15px;
    height: 15px;
    padding: 1px;
}
.menu__icon-image{
    width: 100%;
    height: 100%;
    fill: white;
    transition: 0.3s ease;
}
.menu__icon-image rect{
    fill: red;
}


.menu__footer{
    position: absolute;
    bottom: 10px;
    left: 50%;
    transform: translateX(-50%);
}

.mapboxgl-popup{
    width:350px !important
}
.mapboxgl-popup p{
    text-align: left;
}

</style>
