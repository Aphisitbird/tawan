<template>
    <div class="box"> 
        <center><font  size="30">ระบบคำนวณตั๋วหนัง</font></center>
        <h3 class="title">Room: {{ roomI }}</h3>
        <p>Count :: {{ status.count }}, Price: {{ status.price }}</p>
        <room @chooseRoom="handleChooseRoom" :roomId="roomI"></room>
        <seat 
            :roomId="roomI" 
            @chooseSeat="handleChooseSeat"
            :selectSeats="selectSeats"
            :firebaseSeats="firebaseSeats"
        ></seat>
    </div>
</template>

<script>
import firebase from 'firebase'
import lodash from 'lodash'

import Room from 'Components/Room.vue'
import Seat from 'Components/Seat.vue'
import { pushToArray } from 'Others/lib'

const config = {
    databaseURL: "https://vue-firebase-63021.firebaseio.com"
}

firebase.initializeApp(config)

const db =  firebase.database()

export default {
    components: { Room, Seat },
    data(){
        return {
            roomI: '',
            selectSeats: [],
            firebaseSeats: [],
            status : { count :0,price :0}
        }
    },
    methods: {
        handleChooseRoom(roomId){
            if(this.status.count){
                if(confirm('Data will be lost?')){
                    this.status = {count :0,price :0}
                    this.selectSeats = []
                }else{
                    return false
                }
            }
            this.roomI = roomId

            const roomRef = db.ref().child(this.roomI)
            roomRef.on('value',snapshot => {
               
                const seats =  snapshot.val()
                this.firebaseSeats = []
                _.forOwn(seats, s=> {
                    pushToArray(s,this.firebaseSeats)
                })

                
            })
        },
        handleChooseSeat(seat){
       
           //const ids = this.selectSeats.map(s => s.id)
           //const idx = ids.indexOf(seat.id)

           //if(idx === -1){
           //    this.selectSeats.push(seat)
           //}else{
           //    this.selectSeats.splice(idx,1)
           //}

           pushToArray(seat,this.selectSeats)

           const roomRef = db.ref().child(this.roomI)
           roomRef.push(seat)

           this.status = this.selectSeats.reduce((summary, s) =>{
                summary.count ++
                summary.price += s.price
                return summary
           },{count:0,price:0})

        }
    }
}
</script>