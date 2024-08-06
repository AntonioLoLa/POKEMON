<script>
import Map from '../base_components/Map.vue';
import {blocks,isBlockWalkable} from '../globals.js';


export default {
  components: {
    Map,
  },
  props: ['currentPlayerX', 'currentPlayerY','onMovementDown','onMovementRight','onMovementLeft','onMovementUp', 'inWildEncounter', 
  'inMenu', 'buttonMenu', 'inPokedex', 'buttonA', 'direction'],
  emits: ['update:inWildEncounter', 'update:currentPlayerX', 'update:currentPlayerY','update:inMenu','isMapMoving', 'canUnown','inGrass' ],
  data() {
        return {
          isMapMoving:false,
          canUnown:false,
          finishedMoving:true,
          inGrass:false,
        }
    },
  methods: {
    handleMoving(){ 
        this.finishedMoving=false;
        this.$emit('isMapMoving', true);
        console.log("MOVING " + this.isMapMoving);
    },
    handleFinished(){ 
      
        this.finishedMoving=true;
        this.$emit('isMapMoving', false);
        console.log("Movement FINISHED");

        if(this.onMovementDown){//caso en que sigamos clickando el boton
          this.$emit('update:currentPlayerY', this.changePosition(true, this.currentPlayerY, this.currentPlayerX, 1, 'y'));
        }else if(this.onMovementUp){
          this.$emit('update:currentPlayerY', this.changePosition(true, this.currentPlayerY, this.currentPlayerX, -1, 'y'));
        }else if(this.onMovementLeft){
          this.$emit('update:currentPlayerX', this.changePosition(true, this.currentPlayerY, this.currentPlayerX, -1, 'x'))
        }else if(this.onMovementRight){
          this.$emit('update:currentPlayerX', this.changePosition(true, this.currentPlayerY, this.currentPlayerX, 1, 'x'))
        }
      
      
    },
    handleEnteredGrass() {
      //manejar casos
      this.inGrass=true;
      this.$emit('inGrass',true);
        fetch('http://localhost:8081/enter_grass')
          .then(response => {
            if (response.status === 400) {
              console.log("Wild Encounter");
              this.$emit('update:inWildEncounter',true);
            }
          })
          .catch(error => {
            console.error('Error entering grass:', error);
          });
      
    },
    handleExitedGrass() {
      fetch('http://localhost:8081/leave_grass')
      .then(()=>{
        this.inGrass=false;
        this.$emit('inGrass',false);
      })
      .catch(() => {
          console.log('Leaving grass');
        });
    },
    changePosition(click, Y, X, step, whichMoving){
      let positionMoving= whichMoving=='x'?X:Y;
      console.log("aaaaaaaaaaaaa"+this.onMovementDown + " "+ this.onMovementUp + " "+this.onMovementLeft+ " "+this.onMovementRight+" "+this.isMapMoving);
        if(click && this.finishedMoving  && !this.inWildEncounter && !this.inMenu && !this.inPokedex){
            let canMove= whichMoving=='x'? isBlockWalkable[blocks[Y][X+step]] : isBlockWalkable[blocks[Y+step][X]]
            if(canMove){
              positionMoving+=step;
            }
        }
        return positionMoving;
    },

  },
  watch: {
    buttonA(newValue){
      if(newValue && !this.inWildEncounter && !this.inMenu && !this.inPokedex){
        console.log(blocks[this.currentPlayerY+1][this.currentPlayerX]);
        if((blocks[this.currentPlayerY+1][this.currentPlayerX]==10 && this.direction == 'south') ||
          (blocks[this.currentPlayerY][this.currentPlayerX+1]==10 && this.direction == 'east') ||
          (blocks[this.currentPlayerY-1][this.currentPlayerX]==10 && this.direction == 'north')){
          fetch('http://localhost:8081/my_status_code_is_unknown')
          .then(response => {
            if (response.status === 201) {
              console.log("Unown Encounter");
              this.$emit('canUnown',true);
              
              this.$emit('update:inWildEncounter',true);
            }
          })
          .catch(error => {
            console.error('Error with Unown');
          });
        }
      }
    },
     onMovementDown(newValue) {   
        //newValue?this.intervalID=setInterval(()=>
        //this.$emit('update:currentPlayerY', this.changePosition(newValue, this.currentPlayerY, this.currentPlayerX, 1, 'y')),25):clearInterval(this.intervalID);    
            if(newValue){
              this.$emit('update:currentPlayerY', this.changePosition(newValue, this.currentPlayerY, this.currentPlayerX, 1, 'y'));
            }   
      },   
      onMovementUp(newValue) {
         if(newValue ){
            this.$emit('update:currentPlayerY', this.changePosition(newValue, this.currentPlayerY, this.currentPlayerX, -1, 'y'))
          }
      },
      onMovementLeft(newValue) {
         if(newValue ){
            this.$emit('update:currentPlayerX', this.changePosition(newValue, this.currentPlayerY, this.currentPlayerX, -1, 'x'))
          }
      },
      onMovementRight(newValue) {
          if(newValue ){
            this.$emit('update:currentPlayerX', this.changePosition(newValue, this.currentPlayerY, this.currentPlayerX, 1, 'x'))
          }
      },  
      buttonMenu(newValue){
        if(newValue && !this.inWildEncounter){
          this.$emit('update:inMenu',true);
        }
      }
  },

  
};
</script>

<template>
  <Map :playerX="currentPlayerX" :playerY="currentPlayerY"
       @onStartedMoving="handleMoving"
       @onFinishedMoving="handleFinished"
       @onEnteredGrass="handleEnteredGrass"
       @onExitedGrass="handleExitedGrass">
  </Map>
</template>

  