<template>
  <div
    :style="{ width: size + 'px' }"
    class="menu"
    @mouseenter="onMouseEnter"
    @mouseleave="onMouseLeave"
    
  >
    <div @click="onMouseClick" class="menu-lines">
        <MenuLine
            v-for="(item, index) in items"
            :key="item.id"
            ref="polys"
            :item="item"
            :color="color"
            :line="item.line"
            :index="index"
            :size="size"
            ></MenuLine>
    </div>
    
    <MenuClose @click.native="onCloseClick" :showClose="showClose" :size="size"/>
  </div>
</template>

<script>
import MenuLine from "./MenuLine.vue";
import MenuClose from "./MenuClose.vue";
import { TweenMax, TimelineMax, Sine, Circ } from "gsap";
import '../MorphSVGPlugin.min.js';
import '../DrawSVG.js';

export default {
  name: "MenuBurger",
 
  components: {
    MenuLine,
    MenuClose
  },
  mounted(){


        this.closeOne = document.querySelectorAll('.close-one');
        this.closeTwo = document.querySelectorAll('.close-two');

        this.addAndPosLines();

        let tl = new TimelineMax({
            onComplete: () => {
                for(var index in this.items)
                    this.items[index].line.showLine = false;

                // morph polygons from lines
                let tl2 = new TimelineMax({
                });
                tl2.add('start')
                this.$refs.polys.forEach((item, index) => {
                    // console.log(this.duration)
                    tl2.to(item.$el.firstChild, this.duration, {
                        morphSVG: this.items[index].morph.begin,
                        precompiled: this.items[index].morph.begin
                    },'start');
                });
            }
        });
        
        // stagger lines in
        tl.staggerTo(this.lines, this.duration + 0.1 , {
            drawSVG: "0% 100%"
        },0.1);
        
        
        
  },

  methods: {
    addAndPosLines(){
        this.lines = [];

        

        this.$refs.polys.forEach((item, index) => {
         
            var bb = this.getBBox(item.$el.firstChild, true);

            this.items[index].line.strokeWidth = 2;
            this.items[index].line.y = bb.y + this.items[index].line.strokeWidth/2;

            this.items[index].line.x1 = bb.x;        
            this.items[index].line.x2 = bb.x + bb.width; 
            this.lines.push(this.$refs.polys[index].$el.querySelectorAll('line.polyline'))
            
        });
        
        // initial hide of lines
        TweenMax.set(this.lines, {
            drawSVG: "0% 0%"
        });
    },
      /**
     * @param {SVGElement} element - Element to get the bounding box for
     * @param {boolean} [withoutTransforms=false] - If true, transforms will not be calculated
     * @param {SVGElement} [toElement] - Element to calculate bounding box relative to
     * @returns {SVGRect} Coordinates and dimensions of the real bounding box
     */
    getBBox(element, withoutTransforms, toElement) {
    
        var svg = document.querySelectorAll('.menu')[0];
        
        if (!svg) {
            return { x: 0, y: 0, width: 0, height: 0 };
        }
            
        if (withoutTransforms) {
            return element.getBBox();
        }
        
        var p = svg.createSVGPoint();
        var r = element.getBBox();     
            
        var matrix = (toElement || svg).getScreenCTM().inverse().multiply(element.getScreenCTM()); 

        p.x = r.x;
        p.y = r.y;
        var a = p.matrixTransform(matrix);

        p.x = r.x + r.width;
        p.y = r.y;
        var b = p.matrixTransform(matrix);

        p.x = r.x + r.width;
        p.y = r.y + r.height;
        var c = p.matrixTransform(matrix);

        p.x = r.x;
        p.y = r.y + r.height;
        var d = p.matrixTransform(matrix);

        var minX = Math.min(a.x, b.x, c.x, d.x);
        var maxX = Math.max(a.x, b.x, c.x, d.x);
        var minY = Math.min(a.y, b.y, c.y, d.y);
        var maxY = Math.max(a.y, b.y, c.y, d.y);
            
        var width = maxX - minX;
        var height = maxY - minY;
        
        return {
            x: minX,
            y: minY,
            width: width,
            height: height,        
            cx: minX + width / 2,
            cy: minY + height / 2
        };
    },
    onMouseEnter() {
        this.mouseIn = true;
    //   console.log("enter", this);
      this.$refs.polys.forEach((item, index) => {
        TweenMax.to(item.$el.firstChild, this.duration, {
          morphSVG: this.items[index].morph.end
        });
      });
    },
    onMouseLeave() {
        this.mouseIn = false;
      this.$refs.polys.forEach((item, index) => {
        TweenMax.to(item.$el.firstChild, this.duration + 0.15, {
          morphSVG: this.items[index].morph.begin
        });
      });
      
    },
    onMouseClick(){

        for(var index in this.items)
            this.items[index].line.showLine = true;

        var tl = new TimelineMax({
        });
        global.EventBus.$emit('showGooeyBg', false);
        tl.staggerTo(this.lines, this.duration, {
            drawSVG: "0% -100%"
        },0.1);
        tl.call(this.showCloseBtn, null, this, '-=0.1');
    },
    onCloseClick(){
        console.log('close click')
        if(this.showClose){

            var tl = new TimelineMax({
            });
            
            const closeLines = [ this.closeTwo, this.closeOne];
            global.EventBus.$emit('showGooeyBg', true);

            tl.staggerTo(closeLines, this.duration - 0.1, {
                drawSVG: "0% 0%",
                ease: Circ.easeOut
            },0.1)
            tl.staggerTo(this.lines, this.duration, {
                drawSVG: "0% 100%",
                ease: Circ.easeOut
            },0.2);
            tl.call(this.hideCloseBtn, null, this);
            
        }
    },
    showCloseBtn(){
        const closeLines = [ this.closeTwo, this.closeOne];
        TweenMax.set(closeLines, {
            drawSVG: "0% 0%"
        })
        this.showClose = true;

        var tl = new TimelineMax();
        
        tl.staggerTo(closeLines, this.duration, {
            drawSVG: "0% 100%",
            ease: Sine.easeIn,
            stroke: 'white'
        },0.18)
        // tl.call(() => {
        //     global.EventBus.$emit('showGooeyBg', true);
        // })


    },
    hideCloseBtn(){
        

        this.showClose = false;
        for(var index in this.items)
            this.items[index].line.showLine = false;
            
        // set morph initial line form                    
        this.$refs.polys.forEach((item, index) => {
            TweenMax.set(item.$el.firstChild, {
                morphSVG: this.items[index].morph.end
            });
        });

        // if mouse is not over the burger, just morph to stack
        if(!this.mouseIn){
            this.onMouseLeave();
        }

        
    }
  },
  data() {
    return {
      lines: [],
      size: 80,
      duration: 0.25,
      color: "black",
      showClose: false,
      items: [
        {
          id: "menu-btn-top",
          morph: {
            begin:
              "13.6,0 13.4,0 13.2,0.1 13,0.2 0.6,6.4 0.4,6.6 0.2,6.8 0.1,7.2 0.1,7.5 0.2,7.8 0.4,8 0.6,8.2 12.9,14.9 13,15 13.2,15 13.3,15 13.5,15 13.7,15 13.9,14.9 19.9,11.7 18.8,10 13.4,12.9 3.2,7.3 13.6,2.1 23.8,7.3 17.9,10.5 18.9,12.2 26.4,8.2 26.6,8 26.7,7.8 26.9,7.5 26.9,7.3 26.9,7 26.8,6.8 26.6,6.5 26.4,6.4 14,0.1 13.8,0.1 13.7,0",
            end:
              "13.6,6 12.6,6 11.6,6 10.2,6 0.5,6 0,6 0,6.4 0,6.9 0,7.2 0,7.5 0,8 0.5,8 10.2,8 11.4,8 12.2,8 12.9,8 13.5,8 15.2,8 17,8 19,8 18.6,6.9 13.4,6.8 3.2,7 13.6,7.5 23.8,7 17.6,6.9 18,8 26.5,8 27,8 27,7.6 27,7.3 27,7 27,6.7 27,6.4 27,6 26.5,6 17,6 15.7,6 14.6,6"
          },
          line: {
                x1: 0,
                x2:0,
                y: 0,
                width: 0,
                height: 0,
                strokeWidth: 0,
                stroke: 'black',
                showLine: true
                
          }
        },
        {
          id: "menu-btn-middle",
          morph: {
            begin:
              "3.3,11.3 5.4,12.4 3.2,13.5 13.4,19 23.8,13.4 21.8,12.3 23.8,11.2 26.3,12.5 26.6,12.8 26.9,13.1 26.9,13.4 26.9,13.7 26.8,14 26.5,14.3 13.9,21 13.7,21.1 13.4,21.1 13.2,21.1 13,21.1 12.9,21 0.6,14.3 0.3,14.1 0.2,13.9 0.1,13.7 0.1,13.5 0.1,13.2 0.1,13 0.3,12.8 0.5,12.6",
            end:
              "5.2,12 5.2,14 3.1,12 13.4,12 24,12 23.1,14 23.1,12 26.6,12 27,12 27,12.5 27,13 27,13.4 27,14 26.6,14 16.1,14 14.8,14 13.4,14 12.6,14 11.7,14 10.5,14 0.4,14 0,14 0,13.6 0,13.2 0,13 0,12.7 0,12.4 0,12 0.4,12"
          },
          line: {
                x1: 0,
                x2:0,
                y: 0,
                width: 0,
                height: 0,
                strokeWidth: 0,
                stroke: 'black',
                showLine: true
          }
        },
        {
          id: "menu-btn-bottom",
          morph: {
            begin:
              "3.3,17.1 5.4,18.2 3.2,19.3 13.4,24.8 23.8,19.2 21.8,18.1 23.8,17 26.3,18.3 26.6,18.6 26.9,18.9 26.9,19.2 26.9,19.5 26.8,19.8 26.5,20.1 13.9,26.8 13.7,26.9 13.4,26.9 13.2,26.9 13,26.9 12.9,26.8 0.6,20.1 0.3,19.9 0.2,19.7 0.1,19.5 0.1,19.3 0.1,19 0.1,18.8 0.3,18.6 0.5,18.4",
            end:
              "5.2,18 5.2,20 3.1,18 13.5,18 24,18 23.1,20 23.1,18 26.6,18 27,18 27,18.5 27,19 27,19.4 27,20 26.6,20 16.1,20 14.8,20 13.5,20 12.6,20 11.8,20 10.5,20 0.4,20 0,20 0,19.6 0,19.2 0,19 0,18.7 0,18.4 0,18 0.4,18"
          },
          line: {
                x1: 0,
                x2:0,
                y: 0,
                width: 0,
                height: 0,
                strokeWidth: 0,
                stroke: 'black',
                showLine: true
          }
        }
      ]
    };
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.menu {
  position: relative;
  cursor: pointer;
  display: block;
  
  display:grid;
  grid-template-columns: 1fr;
}
.menu-btn-middle,
.menu-btn-bottom {
  position: absolute;
  top: 0px;
  left: 0;
}

.menu-lines{
    grid-row-start: 1;
    grid-column-start: 1;
}
</style>
