<template>
    <div id="mapBoxContainer">

    </div>
    <button id="zoomto" class="btn-control" @click="handlerZoomTo">Zoom to bounds</button>
</template>

<script>
import mapboxgl from "mapbox-gl";
import "mapbox-gl/dist/mapbox-gl.css";
import {TOKEN, geojson} from "@/assets/env.local";

export default {
    name:'AppMap',
    el:'#mapBoxContainer',
    data(){
        return{
            map:''
        }
    },
    methods:{
        handlerZoomTo(){
            const coordinates = geojson.features[0].geometry.coordinates[0][0];
            const bounds = new mapboxgl.LngLatBounds(
                coordinates[0],
                coordinates[0]
            );
            for (const coord of coordinates) {
                bounds.extend(coord);
            }
            this.map.fitBounds(bounds, {
                padding: 20
            });
        }
    },
    mounted() {
        mapboxgl.accessToken = TOKEN
        this.map = new mapboxgl.Map({
            container: 'mapBoxContainer',
            'style' : 'mapbox://styles/mapbox/light-v10',
            center:[35.8525241145474, 51.367268418343201],
            zoom:11
        })
        // this.map.fitBounds([[-135, 47], [-60, 25]]);
        this.map.on('load',()=>{
            this.map.addSource('route', {
                'type': 'geojson',
                'data': geojson

            });
            this.map.addLayer({
                'id': 'route',
                'type': 'line',
                'source': 'route',
                'layout': {
                    'line-join': 'round',
                    'line-cap': 'round'
                },
                'paint': {
                    'line-color': 'red',
                    'line-width': 5
                }
            });

        })
    },
}
</script>

<style scoped>
#mapBoxContainer{
    width: 500px;
    height: 500px;
}
</style>