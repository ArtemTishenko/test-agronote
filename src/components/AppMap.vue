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
                'data': geojson,
                generateId: true

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
                    'line-color': [
                        'case',['boolean',['feature-state','hover'], false],'red','green'
                    ],
                    'line-width': 5,

                }
            });
            this.map.addLayer({
                'id': 'state-fills',
                'type': 'fill',
                'source': 'route',
                'layout': {},
                'paint': {
                    'fill-color': '#627BC1',
                    'fill-opacity': [
                        'case',
                        ['boolean', ['feature-state', 'hover'], false], 1, 0.5]
                }
            })
            let hoveredStateId = null;
            this.map.on('mousemove', 'state-fills', (e) => {
                if (e.features.length > 0) {
                    if (hoveredStateId !== null) {
                        this.map.setFeatureState(
                            { source: 'route', id: hoveredStateId },
                            { hover: false }
                        );
                    }
                    hoveredStateId = e.features[0].id;
                    this.map.setFeatureState(
                        { source: 'route', id: hoveredStateId },
                        { hover: true }
                    );
                }
            });
            this.map.on('mouseleave', 'state-fills', () => {
                if (hoveredStateId !== null) {
                    this.map.setFeatureState(
                        { source: 'route', id: hoveredStateId },
                        { hover: false }
                    );
                }
                hoveredStateId = null;
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