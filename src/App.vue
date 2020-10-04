<template>
  <div id="app">
    <!-- <nav id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </nav> -->
    <router-view />
    <footer>
      <div class="footerContainer">
        <small class="copyrightText">All rights reserved to</small>
        <img class="logo" src="logo.png" alt="logo" >
      </div>
    </footer>
  </div>
</template>

<style>
#app {
  font-family: 'PT Mono';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: darkslategrey;

  width: 100vw;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}

body {
  margin: 0;
  font-size: 1.4rem;
}

html {
  font-size: 62.5%;
}

footer {
  padding: 2rem 0 1rem 0;
}

.logo {
  width: 108px;
  height: 23px;
}
.footerContainer {
  max-width: 940px;
  display: flex;
  align-items: flex-start;
  text-align: center;
  margin: auto;
}
.copyrightText {
  padding-top: 5px;
  margin-right: 10rem;
}
</style>
