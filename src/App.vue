<template>
  <div id="app">
    <GooeyBackground :showGooeyBg="showGooeyBg"/>

    <div class="container">
      <menu-burger></menu-burger>
      
      <ul class="menu-items" :class="{ 'is-opened': !showGooeyBg }">
        <li v-for="(menu, index) in menus" :key="index"><a href="#">{{menu.title}}</a></li>
      </ul>
    </div>
    
  </div>
</template>

<script>
import MenuBurger from "./components/MenuBurger";
import GooeyBackground from "./components/GooeyBackground";
export default {
  name: "App",

  data(){
    return {
      menus:[
        { title: 'Introduction'},
        { title: 'Case Studies' },
        { title: 'Experiments'},
        { title: 'Design Thinking' },
        { title: 'Contact Jackson' } 
      ],
      showGooeyBg: true
    }
  },
  mounted(){
    global.EventBus.$on('showGooeyBg', (val) => {
      console.log(val, 'emit changed')
      this.showGooeyBg  = val;
    });
  },
  components: {
    MenuBurger,
    GooeyBackground
  }
};
</script>

<style>

@import url('https://fonts.googleapis.com/css?family=Rozha+One');

#app {
  font-family: 'Rozha One', serif;

  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.container{
  padding: 1vw 1vw 10vw 1vw;
}
.menu-items{
      list-style: none;
    text-decoration: none;
    z-index: 10;
    position: relative;
    opacity: 0;
}

.menu-items a{
  text-decoration: none;
  color: white;
  font-size: 3.6vw;
}

.menu-items li{
  opacity: 0;
  transform: translateY(100%);
  transition: transform 0.3s, opacity 0.3s;
    transition-timing-function: ease-in-out;
}
.menu-items li:nth-of-type(1) {
    transition-delay: 0.25s;
}
.menu-items li:nth-of-type(2) {
    transition-delay: 0.2s;
}
.menu-items li:nth-of-type(3) {
    transition-delay: 0.15s;
}
.menu-items li:nth-of-type(4) {
    transition-delay: 0.1s;
}
.menu-items li:nth-of-type(5) {
    transition-delay: 0.05s;
}

.is-opened{
  opacity: 1;
}
.is-opened li{
    opacity: 1;
    transform: translateY(0%);
    transition-duration: 0.4s;
}

.is-opened li:nth-of-type(1) {
    transition-delay: 0.6s;
}
.is-opened li:nth-of-type(2) {
    transition-delay: 0.7s;
}
.is-opened li:nth-of-type(3) {
    transition-delay: 0.8s;
}
.is-opened li:nth-of-type(4) {
    transition-delay: 0.9s;
}
.is-opened li:nth-of-type(5) {
    transition-delay: 1s;
}


.menu-items li a{
  transition: color 0.3s ease-in-out;
}
.menu-items li a:hover{
    color: #7DCA46;
}

</style>
