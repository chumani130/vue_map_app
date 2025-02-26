<template>
    <div class="home-view">
      <header class="app-header">
        <h1>Vehicle Path Visualizer</h1>
      </header>
      <main class="main-content">
        <div class="map-container">
          <MapView />
        </div>
      </main>
    </div>
  </template>
  
  <script>
  import MapView from "./MapView.vue";
  
  export default {
    name: "HomeView",
    components: { MapView },
  };
  </script>
  
  <style scoped>
  .home-view {
    display: flex;
    flex-direction: column;
    height: 100vh;
  }
  
  /* Header styling */
  .app-header {
    background-color: #2c3e50;
    color: #fff;
    padding: 1rem;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  
  /* Main content layout */
  .main-content {
    flex: 1;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 1rem;
  }
  
  /* Map container */
  .map-container {
    position: relative;
    width: 75vmin;
    height: 75vmin;
    border: 1px solid #ccc;
    box-shadow: 0 0 5px rgba(0, 0, 0, 0.1);
  }
  </style>
  