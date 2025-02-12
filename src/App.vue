<script setup>
import { ref } from 'vue';

let counter = 0;
let limit = 36;
let filteredpokemon = ref([]);
let pokemons = ref([]);
const htmlPokemon = ref('');

function createPokemonElement(pokemon) {
    return `
        <div class="pokemon">
            <div class="id">#${pokemon.id}</div>
            <img src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${pokemon.id}.png" alt="${pokemon.name}">
            <div class="name">${pokemon.name}</div>
        </div>
    `;
}

async function cfetch(URL) {
    try {
        const response = await fetch(URL);
        return await response.json();
    } catch (error) {
        console.error('Error', error);
        return null;
    }
}

function createPromiseList() {
    const pokePromises = [];
    for (let i = 0; i < limit && counter < filteredpokemon.value.length; i++, counter++) {
        const pokemon = filteredpokemon.value[counter];
        pokePromises.push(cfetch(pokemon.url));
    }
    return pokePromises;
}

async function render() {
    const pokeData = await Promise.all(createPromiseList());
    htmlPokemon.value += pokeData
        .filter(pokemon => pokemon)
        .map(pokemon => createPokemonElement(pokemon))
        .join('');
}


cfetch("https://pokeapi.co/api/v2/pokemon/?offset=0&limit=898").then(({ results }) => {
    pokemons.value = results;
    filteredpokemon.value = results;
    render(); 
});

</script>

<template>
 <div class="container">

<div class="navbar">
   <div class="heading">Pokemon API</div>
   <div class="searching">
       <input type="text" class="search" placeholder="Search some Pokemon...">
   </div>
</div>

<div class="content">
    <div class="content_img" v-html="htmlPokemon"></div>
</div>

<button class="loading_page">Load More!</button>
</div>

</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Montserrat:ital,wght@0,100..900;1,100..900&display=swap');

body {
    width: 100vw;
    height: 100vh;
    margin: 0;
    padding: 0;
    font-family: Montserrat, sans-serif;
}

.container {
    width: 100%;
    height: 100%;
}

.navbar {
    width: 100%;
    height: 300px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 20px;
}

.heading {
    font-size: 40px;
}

.searching input {
    width: 500px;
    height: 60px;
    border-radius: 9999px;
    font-size: 20px;
}

.searching input::placeholder {
    padding-left: 10px;
}

.content {
    display: flex;
    justify-content: center;
}

.content_img {
    width: 85%;
    height: calc(100% - 300px);
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 10px;
}

.pokemon {
    width: 250px;
    height: 350px;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    align-items: center;
    background-color: azure;
    border-radius: 20px;
}

.pokemon img {
    width: 220px;
    height: 230px;
}

.pokemon .id {
    font-size: 16px;
}

.pokemon .name {
    font-size: 25px;
    font-weight: 700;
}

.pokemon .type {
    height: 50px;
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 20px;
}

button {
    width: 170px;
    height: 60px;
    margin-top: 30px;
    margin-left: 50%;
    transform: translateX(-50%);
    font-size: 25px;
    background-color: red;
    border-radius: 8px;
    border: none;
    cursor: pointer;
}

</style>
