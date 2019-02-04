<template>
  <svg :class="{ 'showLine': line.showLine, 'showClose': line.showClose, [item.id] : true, 'line-master': true} " :id="item.id" :width="size" viewBox="0 0 27 27">
      
    <polygon :fill="color" :points="item.morph.end"  :id="item.id" ref="poly"></polygon>
    <line 
        :x1="line.x1" 
        :y1="line.y" 
        :x2="line.x2" 
        :y2="line.y" 
        :stroke-width="line.strokeWidth" 
        class="polyline"
        :stroke="line.stroke" ref="polyline"/>

  </svg>
</template>

<script>

import { TimelineMax } from "gsap";
export default {
    name: "MenuLine",
    props: ["item", "color", "size", "index", "line"],
    
    mounted(){
        
    },
    methods:{
        
    }
};
</script>

<style>
    .line-master line{
        opacity: 0;
    }
    .showLine  line{

        opacity: 1;
    }
    .showLine polygon{
        opacity: 0;
    }
</style>
