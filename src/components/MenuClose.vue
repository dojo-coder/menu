<template>
    <svg class="close-icon" :width="size" v-show="showClose" viewBox="0 0 27 27">
        <g>
            <line class="close-one" x1="0" y1="0" x2="19.1" y2="19.1" stroke="black" stroke-width="2"/>
            <line class="close-two" x1="19.1" y1="0" x2="0" y2="19.1" stroke="black" stroke-width="2"/>
        </g>
    </svg>
</template>

<script>
export default {
    props:['size', 'showClose'],
    methods:{
        animateIn(){

        },
        animateOut(){

        }
    }
}
</script>

<style>

.close-icon{
    z-index: 2;
    grid-row-start: 1;
    grid-column-start: 1;
}

.close-icon line{
    stroke: black;
}

.close-icon g{
    transform: translateX(4px) translateY(3px);
}

</style>
