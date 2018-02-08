<template>
    <div class="box">
        <h3 class="title">{{ roomId }}</h3>
        <div class="columns">
            <div v-for="r in room" :class="className(r.id)" @click="chooseRoom(r.id)">
                <figure class="image">
                    <img :src="imgSrc(r.id)" >
                </figure>        
            </div>
        </div>
    </div>
</template>

<script>
import { room } from 'Others/room.json'

export default{
    props: [ 'roomId' ],
    data(){
        return {
            room
        }
    },
    methods:{
        imgSrc(id) {
            return `/picture/${ id }.jpg`
        },
        chooseRoom(id){
            this.$emit('chooseRoom',id)
        },
        className(id) {
            return [
                'column','pointer',
                {'chosen': this.roomId === id}
            ]
        }
    },
    mounted() {
        this.chooseRoom(room[0].id)
    }
}
</script>

<style>
.pointer {
    cursor:pointer;
}

.chosen {
    border-style:solid;
}
</style>