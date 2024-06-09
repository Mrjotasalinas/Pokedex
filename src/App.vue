<template>
  <div class="todo">
    <div class="pokeball"></div>
    <q-layout view="hHh lpR fFf">
      <q-header elevated class="bg-red text-white custom-header">
        <q-toolbar>
          <q-toolbar-title>
            <span class="title-image">
              <img src="https://raw.githubusercontent.com/PokeAPI/media/master/logo/pokeapi_256.png" alt="Pokedex Title" class="title-img">
            </span>
          </q-toolbar-title>
        </q-toolbar>
      </q-header>
      <q-page-container>
        <div class="barra">
          <input
            class="busqueda"
            placeholder="Buscar Pokémon Por Nombre O ID"
            v-model="searchQuery"
            @keyup.enter="traer"
          />
          <q-btn label="Buscar" @click="traer" />
        </div>
        <div class="pokeball" v-if="showAnimation"></div>

        <div v-if="imgPokemon" class="pokemon-container">
          <div class="id-container">
            <p class="id-text">#{{ pokemonId }}</p>
          </div>
          <img :src="imgPokemon" alt="Pokemon" class="imagen-pokemon">
          <div class="pokemon-info">
            <q-btn id="custom-btn" 
              v-for="type in types" 
              :key="type" 
              :label="type" 
              :style="{ backgroundColor: tipoColorMap[type], color: 'black' }"
            />
          </div>
          <div class="pokemon-info-grid">
            <p>VIDA: <p>{{ hp }}</p></p>
            <q-linear-progress size="15px" :value="hp / 100" color="green" track-color="grey-15" />
            <h3 class="pokemon-name">{{ nombre }}</h3>
            <p>ATAQUE: <p>{{ attack }}</p></p>
            <q-linear-progress size="15px" :value="attack / 100" color="red" track-color="grey-15" />
            <p>ATAQUE ESPECIAL : <p>{{ specialAttack }}</p></p>
            <q-linear-progress size="15px" :value="specialAttack / 100" color="purple" track-color="grey-15" />
            <p class="pokemon-height">Estatura: {{ height }} M</p>
            <p>DEFENSA: <p>{{ defense }}</p></p>
            <q-linear-progress size="15px" :value="defense / 100" color="blue" track-color="grey-15" />
            <p>DEFENSA ESPECIAL : <p>{{ specialDefense }}</p></p>
            <q-linear-progress size="15px" :value="specialDefense / 100" color="teal" track-color="grey-15" />
            <p class="pokemon-weight">Peso: {{ weight }} Kg</p>
            <p>VELOCIDAD: <p>{{ speed }}</p></p>
            <q-linear-progress size="15px" :value="speed / 100" color="orange" track-color="grey-15" />
          </div>
        </div>
      </q-page-container>
    </q-layout>
  </div>
</template>

<script setup>
import { ref, watch, onMounted} from 'vue';
import axios from 'axios';

const searchQuery = ref('');
const nombre = ref('');
const imgPokemon = ref('');
const pokemonId = ref('');
const height = ref('');
const weight = ref('');
const hp = ref('');
const attack = ref('');
const specialAttack = ref('');
const defense = ref('');
const specialDefense = ref('');
const speed = ref('');
const types = ref([]);

const tipoColorMap = {
  normal: '#FFE6CC',
  fighting: '#FFCCCC',
  flying: '#CCE6FF',
  poison: '#FFCCFF',
  ground: '#FFE6CC',
  rock: '#FFFFCC',
  bug: '#CCFFCC',
  ghost: '#CCCCFF',
  steel: '#E6E6FF',
  fire: '#FFD9B3',
  water: '#B3D9FF',
  grass: '#D9FFB3',
  electric: '#FFFF99',
  psychic: '#FFB3FF',
  ice: '#99FFFF',
  dragon: '#99B3FF',
  dark: '#B3B3CC',
  fairy: '#FFB3D9'
};




async function traer() {
  try {
    const res = await axios.get(`https://pokeapi.co/api/v2/pokemon/${searchQuery.value.toLowerCase()}`);
    imgPokemon.value = res.data.sprites.other['official-artwork'].front_default;
    nombre.value = res.data.name;
    height.value = (res.data.height / 10).toFixed(2);
    weight.value = (res.data.weight / 10).toFixed(2);
    pokemonId.value = res.data.id;

    const stats = res.data.stats;
    hp.value = stats.find(stat => stat.stat.name === 'hp').base_stat;
    attack.value = stats.find(stat => stat.stat.name === 'attack').base_stat;
    specialAttack.value = stats.find(stat => stat.stat.name === 'special-attack').base_stat;
    defense.value = stats.find(stat => stat.stat.name === 'defense').base_stat;
    specialDefense.value = stats.find(stat => stat.stat.name === 'special-defense').base_stat;
    speed.value = stats.find(stat => stat.stat.name === 'speed').base_stat;

    types.value = res.data.types.map(typeInfo => typeInfo.type.name);
  } catch (error) {
    console.error('Error fetching Pokémon data:', error);
    imgPokemon.value = '';
    height.value = '';
    weight.value = '';
    hp.value = '';
    attack.value = '';
    specialAttack.value = '';
    defense.value = '';
    specialDefense.value = '';
    speed.value = '';
    types.value = [];
  }
}

watch(types, (newTypes) => {
  if (newTypes.length > 0) {
    const tipo = newTypes[0].toLowerCase();
    document.body.style.backgroundColor = tipoColorMap[tipo] || '#ffffff';
  }
});


</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Jaro:opsz@6..72&display=swap');

.todo {
  font-family: "Jaro", sans-serif;
}

#custom-btn {
  border: none;
  border-radius: 1rem;
  padding: 10px 20px;
  font-size: 24px;
  margin: 5px;
  color: white;
}

p {
  font-size: 18px;
}

.busqueda {
  color: #FECA1B;
  width: 20%;
  border-radius: 1rem;
  border: solid 1px #FECA1B;
  background-color: #e53935;
  padding: 15px 30px;
  font-size: 20px;
  box-shadow: 0 2px 2px rgba(0, 0, 0, 0.435);
}

.busqueda::placeholder {
  color: #FECA1B;
}

.custom-header {
  background-color: #e53935;
}

.title-image .title-img {
  height: 55px;
  width: 150px;
  margin-left: 30%;
}

.barra {
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 20px;
}

.q-btn {
  background-color: #e53935;
  color: #FECA1B;
  border: solid 1px #FECA1B;
  border-radius: 1rem;
  padding: 14px 20px;
  font-size: 20px;
  cursor: pointer;
  box-shadow: 0 2px 2px rgba(0, 0, 0, 0.435);
}

.buscar-btn:hover {
  background-color: #eb0f0c;
}

.pokemon-container {
  position: relative;
  text-align: center;
}

.id-container {
  position: absolute;
  top: 15%;
  left: 70%;
  transform: translate(-50%, -50%);
  z-index: -1;
}

.id-text {
  color: white;
  font-size: 250px;
  font-weight: 500;
  text-shadow: 15px 10px 10px #e53835ce;
}

.imagen-pokemon {
  position: relative;
  z-index: 1;
  max-width: 100%;
  height: 40vh;
}

.pokemon-name {
  font-size: 110px;
  color: #e53935;
  margin: 0;
  padding-bottom: 17px;
}

.pokemon-height, .pokemon-weight {
  font-size: 1.2rem;
  color: #333;
}

.pokemon-info-grid {
  display: grid;
  grid-template-columns: 1fr 1fr 3fr 1fr 1fr;
  align-items: center;
  text-align: center;
  margin-top: 20px;
  padding: 20px;
  border-radius: 10px;
  width: 100%;
}

.pokemon-stats p {
  margin: 5px 0;
}

.q-linear-progress {
  margin-bottom: 10px;
}

.pokeball {
  display: none;
  width: 192px;
  height: 192px;
  background: radial-gradient(
      white 16px,
      black 17px 18px,
      white 19px 24px,
      black 25px 32px,
      transparent 33px
    ),
    linear-gradient(to bottom, red 0 80px, black 81px 96px, white 97px 100%);
  border-radius: 50%;
  border: 8px solid black;
  box-shadow: inset -16px -8px 0 0 rgba(0, 0, 0, 0.2);
  animation: fall 0.5s ease-in-out 1s,
    shake 1.25s cubic-bezier(0.36, 0.07, 0.19, 0.97) 1.5s 3,
    catch 0.5s ease-out 5.25s forwards;
}

@keyframes shake {
  0% {
    transform: translateX(0) rotate(0);
  }
  20% {
    transform: translateX(-10px) rotate(-20deg);
  }
  30% {
    transform: translateX(10px) rotate(20deg);
  }
  50% {
    transform: translateX(-10px) rotate(-10deg);
  }
  60% {
    transform: translateX(10px) rotate(10deg);
  }
  100% {
    transform: translateX(0) rotate(0);
  }
}
@keyframes fall {
  0% {
    transform: translateY(-200%);
  }
  60% {
    transform: translateY(0);
  }
  80% {
    transform: translateY(-10%);
  }
  100% {
    transform: translateY(0);
  }
}
@keyframes catch {
  to {
    filter: saturate(0.8) brightness(0.8);
  }
}


</style>
