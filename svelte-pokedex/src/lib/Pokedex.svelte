

<script>
  import { onMount } from "svelte";

  let pokemons = []; // La lista de Pokémon se inicia vacía

  let newId = ""; // Para el ID del nuevo Pokémon
  let newName = ""; // Para el nombre del nuevo Pokémon

  // Llamada a la API para cargar los Pokémon al montar el componente
  onMount(async () => {
    try {
      const response = await fetch("http://localhost:4321/api/pokemon.json");
      if (!response.ok) {
        throw new Error("Error al conectar con la Pokedex");
      }
      const data = await response.json();
      pokemons = data.pokemonList.sort((a, b) => a.id - b.id); // Ordenar por ID
    } catch (error) {   //Si no pudo conectar muestra los primeros 9 pokemon como "muestra" 
      pokemons = [
        { id: 1, name: "Bulbasaur" },
        { id: 2, name: "Ivysaur" },
        { id: 3, name: "Venusaur" },
        { id: 4, name: "Charmander" },
        { id: 5, name: "Charmeleon" },
        { id: 6, name: "Charizard" },
        { id: 7, name: "Squirtle" },
        { id: 8, name: "Wartortle" },
        { id: 9, name: "Blastoise" }
      ];
      console.error("Error al cargar los Pokémon: ", error);
    }
  });

  function addPokemon() {   //En esta funcion usamos trimmedName y numId para hacer las verificaciones facilmente, una vez que pasan el valor es asignado al name e id respectivamente (linea 108)
    const trimmedName = newName.trim(); 
    const numId = Number(newId);

    const isValidName = /^[A-Za-z]+$/.test(trimmedName); // Validar que solo hay letras

    if (!isValidName) {
      alert("Por favor, introduce un nombre válido para el Pokémon.");
      return;
    }
    if (isNaN(numId) || numId <= 0 || numId > 999) {
      alert("El ID debe ser un número entre 1 y 999.");
      return;
    }
    if (pokemons.some((p) => p.name.toLowerCase() === trimmedName.toLowerCase())) {
      alert("El nombre del Pokémon ya está en uso. Por favor, elige otro nombre.");
      return;
    }
    if (pokemons.some((p) => p.id === numId)) {
      alert("El ID del Pokémon ya está en uso. Por favor, elige otro ID.");
      return;
    }
    
    pokemons = [...pokemons, { id: numId, name: trimmedName }]; //Agregar al array el pokemon nuevo con su id y nombre ya validado
    pokemons.sort((a, b) => a.id - b.id); // Reordenar la lista después de añadir

    newId = "";
    newName = ""; // Limpiar los campos después de añadir
  }

  function deletePokemon(pokemonId) {
    pokemons = pokemons.filter((p) => p.id !== pokemonId);
  }
</script>


<div class="max-w-2xl mx-auto p-6">
  <div class="flex items-center mb-4">
    <input class="flex-1 p-2 border border-gray-300 rounded mr-2" type="text" bind:value={newId} placeholder="ID" />
    <input class="flex-1 p-2 border border-gray-300 rounded mr-2" type="text" bind:value={newName} placeholder="Nombre" />
    <button class="px-4 py-2 bg-red-500 text-white rounded hover:bg-red-700" on:click={addPokemon}>AGREGAR</button>
  </div>
  <ul class="space-y-2">
    {#each pokemons as pokemon (pokemon.id)}
      <li class="flex justify-between items-center p-3 bg-white rounded shadow">
        <span class ="text-black">{pokemon.id} - {pokemon.name}</span>
        <button class="px-3 py-1 bg-red-600 text-white rounded hover:bg-red-800" on:click={() => deletePokemon(pokemon.id)}>DELETE</button>
      </li>
    {/each}
  </ul>
</div>