<script>
import Menu from '../base_components/Menu.vue';
export default {
    props: ['buttonMenu', 'inMenu', 'onMovementUp', 'onMovementDown', 'buttonA', 'buttonB','playerX', 'playerY',
        'direction', 'pokemonRegister', 'inPokedex'],
    emits: ['update:inMenu', 'update:inPokedex'],
    components: {
        Menu,
    },
    data() {
        return {
            state: 'START',
            selectorPos: 0,
            saving:false,
        }
    },
    watch: {
        buttonMenu(newValue) {
            
            if (newValue && !this.saving) {
                console.log("aaaaa");
                this.$emit('update:inMenu', false)
            }
        },
        onMovementDown() {

            if (this.onMovementDown && !this.saving) {
                this.selectorPos = (this.selectorPos + 1) % 3;
            }

        },
        onMovementUp() {
            if (this.onMovementUp && !this.saving) {
                if (this.selectorPos == 0) {
                    this.selectorPos = 2;
                } else {
                    this.selectorPos = (this.selectorPos - 1) % 3;
                }

            }
        },
        buttonA(newValue) {
            if(newValue && !inWildEncounter){
                if (this.selectorPos == 2 || this.state=='SAVED') {
                this.$emit('update:inMenu', false)
            } else {
                if (this.selectorPos == 1) {
                    this.state = 'SAVING';
                    this.saving=true;
                    let data = {
                        "x": this.playerX,
                        "y": this.playerY,
                        "direction": this.direction,
                        "pokedex": this.pokemonRegister,
                    };
                    fetch('http://localhost:8081/save', {
                        method: 'POST',
                        headers: {
                            'Content-Type': 'application/json'
                        },
                        body: JSON.stringify(data)
                    })
                        .then(() => {
                            this.state = 'SAVED';
                            
                        })
                        .catch(() => {
                            console.error('Error in Saving');
                        });
                }else{
                    this.$emit('update:inMenu',false);
                    this.$emit('update:inPokedex',true);
                }
            }
            }
            
        },
        buttonB(newValue){
            if(newValue){
                this.$emit('update:inMenu', false)
            }
            
        }
    },
}
</script>

<template>
    <Menu :state="state" :selectorPos="selectorPos"></Menu>
</template>