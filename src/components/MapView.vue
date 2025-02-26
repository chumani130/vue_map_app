<template>
    <div id="map" style="height: 500px;"></div>
  </template>
  
  <script>
  import L from 'leaflet';
  import 'leaflet/dist/leaflet.css';
  import Papa from 'papaparse';
  
  export default {
    data() {
      return {
        map: null,
        vehiclePath: [],
        stores: []
      };
    },
    mounted() {
      this.initializeMap();
      this.loadVehiclePath();
      this.loadStoreLocations();
    },
    methods: {
      initializeMap() {
        console.log('Initializing map...');
        this.map = L.map('map').setView([-33.91861, 18.42322], 10);
  
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
          attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        }).addTo(this.map);
      },
  
      loadVehiclePath() {
        console.log('Loading vehicle path...');
        fetch('/data/PathTravelled.json')
          .then(response => response.json())
          .then(data => {
            this.vehiclePath = data;
            console.log('Vehicle path loaded:', this.vehiclePath);
            this.plotVehiclePath();
          })
          .catch(error => console.error('Error loading vehicle path:', error));
      },
  
      loadStoreLocations() {
        console.log('Loading store locations...');
        fetch('/data/storesCopy.csv')
          .then(response => response.text())
          .then(csvText => {
            Papa.parse(csvText, {
              complete: (result) => {
                this.stores = result.data.map(row => ({
                  Store: row.Store,
                  Latitude: parseFloat(row.Latitude),
                  Longitude: parseFloat(row.Longitude)
                }));
                console.log('Store locations loaded:', this.stores);
                this.plotStoresOnMap();
              },
              header: true
            });
          })
          .catch(error => console.error('Error loading store locations:', error));
      },
      
  
      plotVehiclePath() {
        if (!this.vehiclePath.length) return;
  
        this.vehiclePath.forEach(point => {
    L.circleMarker([point.latitude, point.longitude], {
      radius: 5,
      fillColor: 'blue',
      color: 'blue',
      weight: 1,
      opacity: 1,
      fillOpacity: 0.8
    })
    .addTo(this.map)
    .bindPopup(`
      <strong>Speed:</strong> ${point.speed} km/h<br>
      <strong>Heading:</strong> ${point.heading}°<br>
      <strong>Time:</strong> ${new Date(point.timeStamp * 1000).toLocaleString()}
    `);
  });

  console.log('Vehicle path plotted on map.');
      },

      calculateDistance(lat1, lon1, lat2, lon2) {
      const R = 6371; // Radius of Earth in km
      const dLat = (lat2 - lat1) * (Math.PI / 180);
      const dLon = (lon2 - lon1) * (Math.PI / 180);
      const a = 
        Math.sin(dLat / 2) * Math.sin(dLat / 2) +
        Math.cos(lat1 * (Math.PI / 180)) * Math.cos(lat2 * (Math.PI / 180)) * 
        Math.sin(dLon / 2) * Math.sin(dLon / 2);
      const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
      return R * c; // Distance in km
    },

      plotStoresOnMap() {
        if (!this.stores.length) return;
  
        this.stores.forEach(store => {
          L.marker([store.Latitude, store.Longitude])
            .addTo(this.map)
            .bindPopup(`<strong>${store.Store}</strong>`);
        });
  
        console.log('Stores plotted on map.');
      }
    }
  };
  </script>
  
  <style scoped>
  #map {
    height: 500px;
    width: 100%;
  }
  </style>
  