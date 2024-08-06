
<script>
import Player from '../base_components/Player.vue';
export default {
    components:{
        Player,
    },
    props:['facingDirection', 'onMovementDown','onMovementRight','onMovementLeft','onMovementUp'
        ,'inMenu', 'inPokedex', 'wildEncounter', 'mapMoving','inGrass'],
    emits:['update:facingDirection'],
    computed:{
        movement(){  
            if(this.inMenu || this.inPokedex || this.wildEncounter){
                return "standing"
            }
            else if((this.onMovementDown || this.onMovementLeft || this.onMovementRight || this.onMovementUp)){
                return "walking"
            }
            return "standing" 
        },
    },
    methods:{
        canChangeDirection(){
            if(this.mapMoving || this.inMenu || this.inPokedex || this.wildEncounter){
                return false;
            }
            return true;
        },
    },
    watch:{
        onMovementDown(newValue) {
            if(newValue && this.canChangeDirection()){
                this.$emit('update:facingDirection','south');
            }
        },
        onMovementUp(newValue) {
            if(newValue && this.canChangeDirection()){
                this.$emit('update:facingDirection','north');
            }
        },
        onMovementLeft(newValue) {
            if(newValue && this.canChangeDirection()){
                this.$emit('update:facingDirection','west');
            }
        },
        onMovementRight(newValue) {
            if(newValue && this.canChangeDirection()){
                this.$emit('update:facingDirection','east');
            }
        },
    }
    
   
}
</script>

<template>
    <Player :facing="facingDirection" :movementType="movement"></Player>
</template>