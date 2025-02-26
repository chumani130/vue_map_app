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
          L.marker([point.latitude, point.longitude], {
            icon: L.divIcon({
              className: 'custom-vehicle-marker',
              html: `<div style="background-color: ${this.getRandomColor()}; width: 12px; height: 12px; border-radius: 50%;"></div>`,
              iconSize: [12, 12]
            })
          })
          .addTo(this.map)
          .bindPopup(`
            <strong>Speed:</strong> ${point.speed} km/h<br>
            <strong>Heading:</strong> ${point.heading}°<br>
            <strong>Time:</strong> ${new Date(point.timeStamp * 1000).toLocaleString()}
          `);
        });
  
        console.log('Vehicle path plotted.');
      },
  
      getRandomColor() {
        const letters = '0123456789ABCDEF';
        return `#${Array.from({ length: 6 }, () => letters[Math.floor(Math.random() * 16)]).join('')}`;
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
  