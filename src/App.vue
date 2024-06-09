<template>
    <div class="search">
      <div id="logo">
        <img src="./assets/logo.png">
      </div>
      <div class="search-container"><q-input
        v-model="pokeSearch"
        label="ID o nombre de Pokemon"
        :error="!!errorMsg"
        :error-message="errorMsg"
        color="white "
        label-color="white"
        text-color="white"
        input-class="white-text"
      />
      <div class="btn-search"><q-btn color="white" text-color="black" label="Buscar" @click="traer" /></div>
      </div>
    </div>
  <div :style="{ backgroundColor: backgroundColor }" class="container">

    <div v-if="loading" class="loading">Cargando...</div>

    <div class="poke-container" v-if="pokeLoaded">
      <div class="pokeImg">
        <div class="imgPoke"><img :src="imgPokemon" alt="" /></div>
        <div class="pokeId">
          <p>#{{ pokeID }}</p>
        </div>
      </div>

      <div class="pokeInfo">
        <p>{{ pokeName }}</p>
        <h5>ID</h5>
        <h6>#{{ pokeID }}</h6>
        <h5>Altura</h5>
        <h6>{{ pokeHeight }}</h6>
        <h5>Peso</h5>
        <h6>{{ pokeWeight }}</h6>
        <div class="tipoPokemon">
          <div class="tipoPokemonDiv"
            v-for="(typeObj, index) in pokeType"
            :key="index"
            :style="{ backgroundColor: getColor(typeObj.type) }"
          >
            <h6 class="tipoPokemonText">{{ typeObj.type }}</h6>
          </div>
        </div>
      </div>
    </div>

    <div class="pokeStats" v-if="pokeLoaded">
      <div class="pokeStat">
        <p>Hp</p>
        <h2>{{ pokeHp }}</h2>
        <q-linear-progress :value="pokeHp / 100" />
      </div>
      <div class="pokeStat">
        <p>Ataque</p>
        <h2>{{ pokeAttack }}</h2>
        <q-linear-progress :value="pokeAttack / 100" color="warning" />
      </div>
      <div class="pokeStat">
        <p>Defensa</p>
        <h2>{{ pokeDefense }}</h2>
        <q-linear-progress :value="pokeDefense / 100" color="secondary" />
      </div>
      <div class="pokeStat">
        <p>Ataque Especial</p>
        <h2>{{ pokeSpecialAttack }}</h2>
        <q-linear-progress
          :value="pokeSpecialAttack / 100"
          rounded
          color="accent"
        />
      </div>
      <div class="pokeStat">
        <p>Defensa Especial</p>
        <h2>{{ pokeSpecialDefense }}</h2>
        <q-linear-progress
          :value="pokeSpecialDefense / 100"
          rounded
          color="purple"
          track-color="orange"
        />
      </div>
      <div class="pokeStat">
        <p>Velocidad</p>
        <h2>{{ pokeSpeed }}</h2>
        <q-linear-progress
          :value="pokeSpeed / 100"
          rounded
          color="purple"
          track-color="orange"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref, watch } from "vue";

let pokeLoaded = ref(false);
let loading = ref(false);
let imgPokemon = ref("");
let pokeSearch = ref("");
let pokeName = ref("");
let pokeID = ref("");
let pokeHeight = ref("");
let pokeWeight = ref("");
let pokeType = ref([]);
let pokeHp = ref("");
let pokeAttack = ref("");
let pokeDefense = ref("");
let pokeSpecialAttack = ref("");
let pokeSpecialDefense = ref("");
let pokeSpeed = ref("");
let errorMsg = ref("");
let backgroundColor = ref("#E83030");

const coloresTipos = {
  normal: "#A8A878",
  fire: "#F08030",
  water: "#6890F0",
  grass: "#78C850",
  electric: "#F8D030",
  ice: "#98D8D8",
  fighting: "#C03028",
  poison: "#A040A0",
  ground: "#E0C068",
  flying: "#A890F0",
  psychic: "#F85888",
  bug: "#A8B820",
  rock: "#B8A038",
  ghost: "#705898",
  dragon: "#7038F8",
  dark: "#705848",
  steel: "#B8B8D0",
};

function getColor(type) {
  return coloresTipos[type] || "white";
}

function updateBackgroundColor() {
  if (pokeType.value.length > 0) {
    const type = pokeType.value[0].type.toLowerCase(); // Obtener el tipo en minúsculas
    const color = coloresTipos[type] || "#E83030";
    backgroundColor.value = color + "cc"; // Agregar 2 puntos de opacidad (hexadecimal)
  } else {
    backgroundColor.value = "#E83030";
  }
}



watch(pokeType, updateBackgroundColor);

async function traer() {
  if (!pokeSearch.value) {
    errorMsg.value = "Ingrese un ID o nombre de Pokemon";
    return;
  }

  loading.value = true;
  try {
    let res = await axios.get(
      `https://pokeapi.co/api/v2/pokemon/${pokeSearch.value}`
    );
    errorMsg.value = "";
    pokeLoaded.value = true;
    imgPokemon.value = res.data.sprites.other["official-artwork"].front_default;
    pokeName.value = res.data.name;
    pokeID.value = res.data.id;
    pokeHeight.value = res.data.height;
    pokeWeight.value = res.data.weight;
    pokeType.value = res.data.types.map((typeName) => ({
      type: typeName.type.name,
    }));
    pokeHp.value = res.data.stats[0].base_stat;
    pokeAttack.value = res.data.stats[1].base_stat;
    pokeDefense.value = res.data.stats[2].base_stat;
    pokeSpecialAttack.value = res.data.stats[3].base_stat;
    pokeSpecialDefense.value = res.data.stats[4].base_stat;
    pokeSpeed.value = res.data.stats[5].base_stat;
  } catch (error) {
    errorMsg.value = "No se encontró el Pokémon. Intente con otro nombre o ID.";
    console.error(error);
  } finally {
    loading.value = false;
  }
}
</script>

<style src="./style.css"></style>

<style scoped>
.container {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  /* padding: 20px; */
  transition: background-color 0.5s ease;
  height: 92vh;
}

.white-text {
  color: white !important;
}


.search {
  background-color: #E83030;
  display: flex;
  justify-content: center;
  align-items: center;
  max-width: 100%;
  margin: auto; /* Centra el div y agrega espacio en la parte inferior */
  z-index: 2;
}

.search #logo {
  width:  10%;
  margin-right: 20px;
}

.search #logo img {
  width: 100%;
  height: auto;
}

.search-container {
  align-items: center;
  display: flex;
  flex-direction: row; /* Ancho completo del contenedor */
  max-width: none; /* Eliminar el ancho máximo si lo tiene */
  color: white;
}


.q-input {
  width: 100% !important;
  max-width: none !important;
}

.loading {
  font-size: 20px;
  color: grey;
}

.poke-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: flex-start;
  gap: 20px;
}

.pokeImg {
  position: relative;
  width: 100%;
  height: auto;
}

.imgPoke {
  position: relative;
  z-index: 2;
}

.imgPoke img {
  width: 60%;
  height: auto;
  display: block;
}

.pokeId {
  position: absolute;
  bottom: -50px;
  right: -30px;
  color: rgba(255, 255, 255, 0.5);
  padding: 5px;
  font-size: 300px;
  font-family: Arial, Helvetica, sans-serif;
  font-weight: bold;
}
.pokeInfo {
  display: flex;
  flex-direction: row;
  align-items: center; /* Centra horizontalmente los elementos */
  gap: 10px;
  color: white;
}

.pokeInfo p {
  font-family: "Gill Sans", "Gill Sans MT", Calibri, "Trebuchet MS", sans-serif;
  font-size: 50pt;
  font-weight: bold;
}

.tipoPokemon {
  display: flex;
  flex-wrap: wrap; /* Permitir que los elementos se envuelvan si no caben en una línea */
  gap: 5px;
  justify-content: center; /* Centra horizontalmente los elementos */
}

.tipoPokemonDiv {
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 5px 10px;
  margin-right: 5px;
  background-color: transparent;
  border-radius: 5px;
  border: 1px solid transparent;
  transition: border-color 0.3s ease;
}

.tipoPokemonDiv:hover {
  border-color: black;
}

.tipoPokemonText {
  margin: 0 5px;
}

.pokeStats {
  border-radius: 25px;
  display: flex;
  flex-direction: column;
  align-items: center; /* Centra horizontalmente el contenido */
  background-color: #f8f8f8;
  width: 30%;
  gap: 1px; /* Espaciado entre los elementos internos */
  margin-left: 40px;
  margin-top: -10px; /* Ajuste de margen superior negativo */
  padding: 25px;
}

.pokeStat {
  padding: 15px; /* Ajuste de margen interno */
  border-radius: 5px;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
  text-align: center;
  width: 100%; /* Ancho completo del contenedor */
  margin-bottom: -10px; /* Ajuste de margen inferior negativo */
}

.pokeStat p {
  font-size: 25px;
  font-weight: bold;
   margin-bottom: -25px;
  color: #555;
}

.pokeStat h2 {
  font-size: 20px;
  margin-bottom: -10px;
  color: #333;
}

.q-linear-progress {
  width: 100%;
  height: 20px;
   margin-bottom: -10px;
}



/* .pokeStat {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
} */

/* .q-linear-progress {
  width: 100%;
} */
</style>
