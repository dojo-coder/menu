<template>
    <svg class="shape-overlays" viewBox="0 0 100 100" preserveAspectRatio="none">
        <defs>
            <linearGradient id="gradient1" x1="0%" y1="0%" x2="0%" y2="100%">
                <stop offset="0%"   stop-color="#3A0066"/>
                <stop offset="100%" stop-color="#ff0ea1"/>
            </linearGradient>
            <linearGradient id="gradient2" x1="0%" y1="0%" x2="0%" y2="100%">
                <stop offset="0%"   stop-color="#7DCA46"/>
                <stop offset="100%" stop-color="#ff3898"/>
            </linearGradient>
            <linearGradient id="gradient3" x1="0%" y1="0%" x2="0%" y2="100%">
                
                <stop offset="0%"   stop-color="#046166"/>
                <stop offset="100%" stop-color="#034e52"/>
            </linearGradient>
        </defs>
        <path class="shape-overlays__path" fill="url(#gradient1)"></path>
        <path class="shape-overlays__path" fill="url(#gradient2)"></path>
        <path class="shape-overlays__path" fill="url(#gradient3)"></path>
        </svg>
</template>

<script>
import {TimelineMax, Power2} from 'gsap'
export default {
    props:['showGooeyBg'],
    data: () => {
        return {
            tl: null,
            numPoints: 3,
            delayPointsMax: 0.3,
            delayPerPath: 0.25,
            duration: 0.6,
            isOpened: false,
            pointsDelay: [],
            allPoints: [],
            ease: Power2.easeInOut,
            overlay:null,
            paths:null
        }

    },
    watch:{
        'showGooeyBg'(val){
            this.onClick();
        }
    },
    computed:{
        numPaths(){
            return this.paths.length
        }
    },
    methods:{
        toggle(){
            this.tl.progress(0).clear();
            for (var i = 0; i < this.numPoints; i++) {
                this.pointsDelay[i] = Math.random() * this.delayPointsMax;
            }
            
            for (var i = 0; i < this.numPaths; i++) {
                var points = this.allPoints[i];
                var pathDelay = this.delayPerPath * (this.isOpened ? i : (this.numPaths - i - 1));
                    
                for (var j = 0; j < this.numPoints; j++) {
                
                var config = {
                    ease: this.ease
                };
                config[j] = 0;
                var delay = this.pointsDelay[j];
                
                this.tl.to(points, this.duration, config, delay + pathDelay);
                // tl.to(points, duration - delay, config, delay + pathDelay);
                }
            }
        },
        onClick(){
            if (!this.tl.isActive()) {
                this.isOpened = !this.isOpened;
                this.toggle();
            }
        },
        render(){
            for (var i = 0; i < this.numPaths; i++) {
                var path = this.paths[i];
                var points = this.allPoints[i];
                
                var d = "";
                d += this.isOpened ? `M 0 0 V ${points[0]} C` : `M 0 ${points[0]} C`;
                
                for (var j = 0; j < this.numPoints - 1; j++) {
                
                var p = (j + 1) / (this.numPoints - 1) * 100;
                var cp = p - (1 / (this.numPoints - 1) * 100) / 2;
                d += ` ${cp} ${points[j]} ${cp} ${points[j+1]} ${p} ${points[j+1]}`;
                }
                
                d += this.isOpened ? ` V 100 H 0` : ` V 0 H 0`;
                path.setAttribute("d", d)
            }  
        }
    },
    mounted(){
        

        this.overlay = document.querySelector(".shape-overlays");
        this.paths = document.querySelectorAll(".shape-overlays__path");



        this.tl = new TimelineMax({ onUpdate: this.render });

        for (var i = 0; i < this.numPaths; i++) {
            var points = [];
            this.allPoints.push(points);
            for (var j = 0; j < this.numPoints; j++) {
                points.push(100);
            }
        }

        this.toggle();

    }
}
</script>

<style>
.shape-overlays {
  width: 100%;
	height: 100%;
	position: fixed;
	top: 0;
	left: 0;
  cursor: pointer;
}
</style>
