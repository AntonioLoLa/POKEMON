<script>
import Wild from '../base_components/WildEncounter.vue';
export default {
    props:["buttonA", "buttonB", 'onMovementUp', 'onMovementDown', 'pokemonRegister', 'inWildEncounter','canUnown'],
    emits:['update:pokemonRegister','update:inWildEncounter'],
    components:{
        Wild,
    },
    data(){
        return{
            currentState:'START',
            selectorPos:0,
            pokemonId:0,
            isPokemonOwned:false,
        }  
    },
    methods:{
        catching(){
            fetch('http://localhost:8081/capture')
                .then(response => {
                    if (response.status === 400) {
                        this.currentState='MAIN';
                    }else{
                        //otherwise retuns 200
                        this.currentState='CAUGHT';                       
                        this.pokemonRegister[this.pokemonId]=true;
                        this.$emit('update:pokemonRegister', this.pokemonRegister)
                    }
                })
                .catch(error => {
                    console.error('Error entering grass:', error);
                });
        },
        buttonSequence(){
            if(this.currentState == 'START'){
                this.currentState='MAIN';
            }else if(this.currentState == 'MAIN'){
                if(!this.selectorPos){
                    this.currentState="CATCHING";  
                    this.catching();
                }else{
                    this.currentState="RUNNING_AWAY";    
                    
                }      
            }else {
                console.log("quiero saliiiir");
                this.$emit('update:inWildEncounter',false)
            }
        },
    },
    watch:{  
        buttonA(){  
            if(this.buttonA){
                this.buttonSequence();
            }
            
        },
        buttonB(){   
            if(this.buttonB && this.currentState == 'START'){
                this.buttonSequence();
            }      
        },
        onMovementUp(){
            
            if(this.onMovementUp && this.currentState == 'MAIN'){
                this.selectorPos=(this.selectorPos+1)%2;
            }
            
        },
        onMovementDown(){
            if(this.onMovementDown && this.currentState == 'MAIN'){
                this.selectorPos=(this.selectorPos+1)%2;
            }
        },
        
    },
    mounted(){
        this.pokemonId = (Math.random()*9) < 5 ? 16 : 19;
        if(this.pokemonRegister != undefined){
            if(Object.keys(this.pokemonRegister).length >= 2 && this.canUnown){
                this.pokemonId = 201;
                this.$emit('canUnown', false);
            }
            if( !(this.pokemonId in this.pokemonRegister)){
            this.pokemonRegister[this.pokemonId]= false
            this.$emit('update:pokemonRegister', this.pokemonRegister)
            this.isPokemonOwned=false;
            }else{
                this.isPokemonOwned= (this.pokemonRegister[this.pokemonId]==true)? true:false;
            }
        }
        

    },
}
</script>

<template>
    <Wild :state="currentState" :selectorPos="selectorPos" :pokemonId="pokemonId" :isPokemonOwned="isPokemonOwned"/>
</template>