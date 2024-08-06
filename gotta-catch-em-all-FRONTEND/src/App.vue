<script>
//base_components
import ControlsFrame from './base_components/ControlsFrame.vue';

//components
import MapMiddleware from './components/MapMiddleware.vue';
import PlayerMiddleware  from './components/PlayerMiddleware.vue';
import MenuMiddleware  from './components/MenuMiddleware.vue';
import WildEncounterMiddleware  from './components/WildEncounterMiddleware.vue';
import PokedexMiddleware  from './components/PokedexMiddleware.vue';

export default {
  name: 'App',
  data() {
    return {
      playerX:undefined,
      playerY:undefined,
      direction:undefined,
      pokemonRegister:undefined,
      onMovementUp:false,
      onMovementDown:false,
      onMovementLeft:false,
      onMovementRight:false,
      onMovementA:false,
      onMovementB:false,
      onButtonMenu:false,
      loaded:false,
      isMapMoving:false,
      inWildEncounter:false,
      inMenu:false,
      inPokedex:false,
      canUnown:false,
      inGrass:false,
    }
  },
  components: {
    ControlsFrame,
    MapMiddleware ,
    PlayerMiddleware ,
    MenuMiddleware ,
    WildEncounterMiddleware ,
    PokedexMiddleware ,
  },
  mounted() {
    fetch('http://localhost:8081/initial_info')
      .then(response => {
        if (!response.ok) {
          //throw new Error('Network response was not ok');
        }
        return response.json();
      })
      .then(data => {
        this.playerX = data.x;
        this.playerY = data.y;
        this.direction = data.direction;
        this.pokemonRegister = data.pokedex;
        
        this.loaded=true;
      })
      .catch(error => {
        console.error('Error al hacer el fetch de /intial_info', error);
      });
  },
};
</script>

<template>
  
    <ControlsFrame @onMovementUp="newValue => this.onMovementUp=newValue"
                   @onMovementDown="newValue => this.onMovementDown=newValue"
                   @onMovementLeft="newValue => this.onMovementLeft=newValue"
                   @onMovementRight="newValue => this.onMovementRight=newValue"
                   @onButtonA="newValue => this.onMovementA=newValue"
                   @onButtonB="newValue => this.onMovementB=newValue"
                   @onButtonMenu="newValue => this.onButtonMenu=newValue"/>
  
    <MapMiddleware  v-if="loaded" 
                    v-model:currentPlayerX="playerX" 
                    v-model:currentPlayerY="playerY"
                    v-model:inWildEncounter="inWildEncounter"
                    v-model:inMenu="inMenu"
                    :inPokedex="inPokedex"
                    :onMovementDown="onMovementDown" 
                    :onMovementUp="onMovementUp" 
                    :onMovementLeft="onMovementLeft" 
                    :onMovementRight="onMovementRight"
                    :buttonMenu="onButtonMenu"
                    :buttonA="onMovementA"
                    :direction="direction"
                    @canUnown="newValue => {this.canUnown=newValue;console.log('unownk'+this.canUnown);}"
                    @isMapMoving="newValue=>this.isMapMoving=newValue"
                    @inGrass="newValue=>this.inGrass=newValue"/>

    <PlayerMiddleware  v-if="loaded" 
                        v-model:facingDirection="direction" 
                        :onMovementDown="onMovementDown" 
                        :onMovementUp="onMovementUp" 
                        :onMovementLeft="onMovementLeft" 
                        :onMovementRight="onMovementRight"
                        :mapMoving="isMapMoving" 
                        :inMenu="inMenu"
                        :inPokedex="inPokedex"
                        :wildEncounter="inWildEncounter"
                        :inGrass="inGrass"
                        />

      <WildEncounterMiddleware v-if="inWildEncounter "
                              v-model:inWildEncounter="inWildEncounter" 
                              :buttonA="onMovementA" 
                              :buttonB="onMovementB" 
                              :onMovementDown="onMovementDown" 
                              :onMovementUp="onMovementUp" 
                              v-model:pokemonRegister="pokemonRegister"
                              :canUnown="canUnown"
                              @canUnown="newValue => this.canUnown=newValue"/>
        
        <MenuMiddleware v-if="inMenu"
                         v-model:inMenu="inMenu" 
                         v-model:inPokedex="inPokedex" 
                         :buttonA="onMovementA" 
                         :buttonB="onMovementB" 
                         :buttonMenu="onButtonMenu"
                         :onMovementDown="onMovementDown" 
                          :onMovementUp="onMovementUp" 
                          :playerX="playerX"
                          :playerY="playerY"
                          :direction="direction"
                          :pokemonRegister="pokemonRegister"/>

        <PokedexMiddleware v-if="inPokedex && !inWildEncounter"
                        :buttonB="onMovementB" 
                        :pokemonRegister="pokemonRegister"
                        v-model:inMenu="inMenu" 
                        v-model:inPokedex="inPokedex" 
        />    
  
  
</template>


