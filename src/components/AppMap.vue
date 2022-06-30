<template>
    <div id="mapBoxContainer" class="mapBoxContainer" :class="mapWidth" >

    </div>
</template>

<script>
import mapboxgl from "mapbox-gl";
import "mapbox-gl/dist/mapbox-gl.css";
import {TOKEN, geojson, poi2} from "@/env.local";

export default {
    name:'AppMap',
    el:'#mapBoxContainer',
    data(){
        return{
            map:'',
            geojson:geojson,
            poi2:poi2,
            widthClass:'',
            centerCoordinates: geojson.features[0].geometry.coordinates[0][0]
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
        initMapBox(){
            // const centerCoordinates = this.geojson.features[0].geometry.coordinates[0][0]

            mapboxgl.accessToken = TOKEN
            this.map = new mapboxgl.Map({
                container: 'mapBoxContainer',
                'style' : 'mapbox://styles/mapbox/light-v10',
                center:[this.centerCoordinates[0][0],this.centerCoordinates[0][1]],
                zoom:11,
            })
            this.map.on('load',()=>{
                this.addMapSource()
                this.addMapLayers()
                this.setMapListeners()
            })
        },
        handlerBtnInspection(){
            const coordinates = this.geojson.features[0].geometry.coordinates[0][0];

            let tempMinLongs =[]
            let tempMaxLongs =[]
            let tempMinLats =[]
            let tempMaxLats =[]
            let sortGeoJson =  JSON.parse(JSON.stringify(this.geojson.features))
            sortGeoJson.forEach((feature)=>{
                let sortCoordinates = feature.geometry.coordinates[0][0]
                sortCoordinates.sort((a,b)=>{
                    return a[0]-b[0]
                })
                tempMinLongs.push(sortCoordinates[0][0])
                tempMaxLongs.push(sortCoordinates[sortCoordinates.length-1][0])

                sortCoordinates.sort((a,b)=>{
                    return a[1]-b[1]
                })
                tempMinLats.push(sortCoordinates[0][1])
                tempMaxLats.push(sortCoordinates[sortCoordinates.length-1][1])


            })
            tempMinLongs.sort((a,b)=>(a-b))
            tempMaxLongs.sort((a,b)=>(b-a))
            tempMinLats.sort((a,b)=>(a-b))
            tempMaxLats.sort((a,b)=>(b-a))


            const bounds = new mapboxgl.LngLatBounds(
                [tempMaxLongs[0],tempMinLats[0]],
                [tempMaxLongs[0],tempMaxLats[0]],
            );

            for (const coord of coordinates) {
                bounds.extend(coord);
            }
            this.map.fitBounds(bounds, {
                padding: 20
            });
        },
        setMapListeners(){
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
                    center: [this.centerCoordinates[0][0],this.centerCoordinates[0][1]],
                });
            });
            this.map.on('click', 'state-fills', (e) => {
                new mapboxgl.Popup()
                    .setLngLat(e.lngLat)
                    .setHTML(
                        `<p>name_rus: ${e.features[0].properties.name_rus}</p>
                         <p>area : ${e.features[0].properties.area}</p>
                         <p>rating : ${e.features[0].properties.rating}</p>`
                    )

                    .addTo(this.map);
            });
        },
        addMapLayers(){
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
                        'case',['boolean',['feature-state','hover'], false],'#267AF8','#92f88f'
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
                    'fill-color': '#92f88f', //TODO
                    'fill-opacity': [
                        'case',
                        ['boolean', ['feature-state', 'hover'], false], 1, 0.3]
                }
            })
            this.map.addLayer({
                'id': 'poi-labels',
                'type': 'symbol',
                'source': 'places',

                'layout': {
                    'text-field': ['get', 'id'],
                    'text-variable-anchor': ['top', 'bottom', 'left', 'right'],
                    'text-radial-offset': 0.5,
                    'text-justify': 'auto',
                },

            })
            this.map.addLayer({
                'id': 'poi-circle',
                'type': 'circle',
                'source': 'places',
                'paint': {
                    'circle-color': '#4b463b',
                    'circle-radius': 6,
                    'circle-stroke-width': 2,
                    'circle-stroke-color': '#ffffff',

                }

            })
        },
        addMapSource(){
            this.map.addSource('route', {
                'type': 'geojson',
                'data': this.geojson,
                generateId: true

            });

            this.map.addSource('places',{
                'type': 'geojson',
                'data': this.poi2,
                generateId: true
            })
        },

    },
    mounted() {
        this.initMapBox()
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