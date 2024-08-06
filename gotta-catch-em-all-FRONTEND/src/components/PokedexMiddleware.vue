<script>
import Pokedex from '../base_components/Pokedex.vue';
import {pokemonNames} from '../globals.js';
export default {
    props: ['pokemonRegister', 'buttonB', 'inPokedex','inMenu','canUnown'],
    emits: ['update:inPokedex','update:inMenu' ],
    components: {
        Pokedex,
    },
    computed: {
        getNPokemonsSeen() {
            if (this.pokemonRegister && typeof this.pokemonRegister === 'object') {
                return Object.keys(this.pokemonRegister).length;
            }
            return 0;
        },

        getNPokemonsOwned() {
            let pokemonsOwned = 0;
            if (this.pokemonRegister !== null && typeof this.pokemonRegister === 'object') {
                for (let key in this.pokemonRegister) {
                    if (this.pokemonRegister[key]) {
                        pokemonsOwned++;
                    }
                }
            }
            return pokemonsOwned;
        },

        getPokemonInfo() {
            let pokemonInfo=[];
            if (this.pokemonRegister !== null && typeof this.pokemonRegister === 'object') {
                for (let key in this.pokemonRegister) {
                    let dictionary={
                        'id':key,
                        'owned':this.pokemonRegister[key],
                        'name':pokemonNames[key]["name"]
                    }
                    pokemonInfo.push(dictionary)

                }
            }
            return pokemonInfo;
        },

    },
    watch:{
        buttonB(newValue){
            if(newValue){
                this.$emit('update:inMenu',true);
                this.$emit('update:inPokedex', false);
                
            }
        }
    }
}
</script>

<template>
    <Pokedex :numberOfSeenPokemons="getNPokemonsSeen" :numberOfOwnedPokemons="getNPokemonsOwned"
        :pokemonsInfo="getPokemonInfo"></Pokedex>
</template>