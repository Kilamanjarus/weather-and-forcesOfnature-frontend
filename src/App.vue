<template>
  <router-view />
</template>

<!-- <head> Attempted to use Open Weather Map API, FAILURE
	<script type="text/javascript" src="leaflet.js"></script>
	<link rel="stylesheet" type="text/css" href="leaflet-openweathermap.css" />
	<script type="text/javascript" src="leaflet-openweathermap.js"></script>
</head> -->

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}
</style>
