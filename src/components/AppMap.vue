<template>
    <div id="mapBoxContainer" class="mapBoxContainer" :class="mapWidth" >

    </div>
    <button id="zoomto" class="btn-control" @click="handlerBtnInspection">Zoom to bounds</button>
</template>

<script>
import mapboxgl from "mapbox-gl";
import "mapbox-gl/dist/mapbox-gl.css";
import {TOKEN, geojson} from "@/env.local";

export default {
    name:'AppMap',
    el:'#mapBoxContainer',
    data(){
        return{
            map:'',
            geojson:geojson,
            widthClass:''
        }
    },
    props:['openMenu'],
    computed:{
        mapWidth(){
            if(this.map){
                let mapDiv = document.querySelector('.wrapper__map');
                let mapContainer = document.querySelector('#mapBoxContainer');
                if(this.openMenu){
                    mapDiv.style.setProperty('width', 'calc(100% - 250px)');
                    mapDiv.style.setProperty('margin-left','250px')
                    mapContainer.style.setProperty('width','calc(100% - 250px)')
                }else{
                    mapDiv.style.setProperty('width', 'calc(100% - 50px)');
                    mapDiv.style.setProperty('margin-left','50px')
                    mapContainer.style.setProperty('width','calc(100% - 50px)')

                }
                this.map.resize()

            }
               return true
        },

    },
    methods:{
        handlerBtnInspection(){
            const coordinates = this.geojson.features[0].geometry.coordinates[0][0];
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
        const centerCoordinates = this.geojson.features[0].geometry.coordinates[0][0]

        mapboxgl.accessToken = TOKEN
        this.map = new mapboxgl.Map({
            container: 'mapBoxContainer',
            'style' : 'mapbox://styles/mapbox/light-v10',
            center:[centerCoordinates[0][0],centerCoordinates[0][1]],
            zoom:11,
            // trackResize:true
        })
        // this.map.fitBounds([[-135, 47], [-60, 25]]);
        this.map.on('load',()=>{
            this.map.addSource('route', {
                'type': 'geojson',
                'data': this.geojson,
                generateId: true

            });
            // console.log(this.map)
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
                filter: ['has', 'rating'],
                'layout': {},
                'paint': {
                    'fill-color': '#bd0000',
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


            this.map.on('resize', () => {
                this.map.flyTo({
                    center: [centerCoordinates[0][0],centerCoordinates[0][1]],
                });
            });

            this.map.on('click', 'state-fills', (e) => {
                console.log(e.features[0].properties)
                new mapboxgl.Popup()
                    .setLngLat(e.lngLat)
                    .setHTML(e.features[0].properties.name_rus)
                    .addTo(this.map);
            });



        })
    },
}
</script>

<style scoped>
.mapBoxContainer{
    position: absolute;
    top: 0;
    bottom: 0;
    width: 100%;
    height: 100vh;
}
.btn-control{
    position: absolute;
    top: 0;
    left: 50%;
    z-index: 9999;
}
</style>