<template>
    <div class="min-h-screen mb-20">
        <h1 class="text-center text-[10vw] md:text-5xl text-white font-bold mt-4" :class="isGameStarted && 'mt-8'">Scrabble
            Scoring</h1>
        <main v-if="isGameStarted">
            <section>
                <div class="row">
                    <div class="container">
                        <h1 class="text-center text-2xl text-white ">{{ players[playerTurn].name }}'s turn</h1>
                        <div class="wrapper flex flex-col md:flex-row gap-2 justify-center mt-4">
                            <input class="px-4 py-2 rounded-[8px]" type="number" v-model="myScore" />

                            <div class="flex gap-4 lg:gap-2 justify-center lg:justify-start"><button
                                    class="bg-blue-800 px-4 py-2 text-white rounded-[8px]" @click="nextTurn">Add
                                    score</button><button class="bg-red-700 rounded-[8px] px-4 py-2 text-white"
                                    @click="nextTurn">Skip
                                    turn</button></div>
                        </div>
                    </div>
                </div>

            </section>

            <section class="mt-10">
                <div class="row">
                    <div class="container">
                        <div class="flex lg:w-1/2 mx-auto justify-center gap-4 lg:gap-8">
                            <div class="text-center w-1/2 border-b-[1px] border-white border-opacity-50 font-bold text-white text-lg lg:text-2xl tracking-[0.5px]"
                                v-for="player in players" :key="player.name">
                                {{ player.name }} </div>
                        </div>
                        <div class="score-wrapper">
                            <div class="score-line  flex lg:w-1/2 mx-auto gap-8">
                                <div class="text-center w-1/2" v-for="player in players" :key="player.scores">
                                    <div class="even:bg-gray-200 odd:text-white" v-for="score in player.scores">{{ score }}
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="row fixed bottom-4 left-1/2 -translate-x-1/2">
                    <div class="container">
                        <button class="block mx-auto bg-red-500 px-4 py-2 text-xl rounded-lg text-white"
                            @click="resetGame">Reset</button>
                    </div>
                </div>
                <div class="row absolute top-4 left-4 ">
                    <div class="container text-white">
                        Game turn : {{ gameTurn + 1 }}
                    </div>
                </div>
            </section>
        </main>
        <main v-else class="mt-20">
            <section>
                <div class="row">
                    <div class="container">
                        <h2 class="text-center text-xl text-white">Add player's name (4 max)</h2>
                    </div>
                </div>
                <div v-if="false" class="row mt-6">
                    <div class="container">
                        <div class="wrapper-number-players flex gap-8 justify-center">
                            <div v-for="(tile, i) in 4" :key="tile" class="wrapper-number relative cursor-pointer group">
                                <div class="number w-[80px] aspect-square border-4 border-black flex justify-center items-center text-3xl font-bold rounded-[10px] bg-green-700 relative z-10 bg-white translate-x-[-8px] group-hover:translate-x-0 group-hover:translate-y-0 transition-transform duration-150 translate-y-[-8px]"
                                    :data-player-number="i + 1" @click.self="setNumberPlayers($event)">
                                    {{ i + 1 }}
                                </div>
                                <div
                                    class="3d w-[80px] aspect-square border-4 border-black rounded-[10px] bg-green-700  absolute top-0 left-0 bg-[#ccc]">
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="row mt-4">
                    <div class="container">
                        <div class="wrapper flex justify-center gap-4">
                            <input type="text" class=" py-2 px-4 rounded-[8px]" ref="newPlayer">
                            <button class="text-white text-2xl" @click="addPlayer"> + </button>
                        </div>
                        <p v-if="isMaxPlayer" class="text-center text-red-200 mt-2">You can't add more players !</p>
                    </div>
                </div>
                <div class="row mt-8">
                    <div class="container">
                        <div class="wrapper flex justify-center">
                            <ul class="">
                                <li class="text-xl text-white flex items-center gap-2" v-for="(player, i) in players"
                                    :key="'player-' + i">

                                    Player {{ i + 1 }} : <span class="capitalize"> {{ player.name }}</span>
                                    <span class="inline-block">
                                        <Trash :data-player="1" @click.native="removePlayer($event)"
                                            class="cursor-pointer" />
                                    </span>
                                </li>
                            </ul>
                        </div>

                    </div>
                </div>


                <div class="row absolute bottom-10 left-1/2 -translate-x-1/2 w-[80%]">
                    <div class="container">
                        <button :class="players.length >= 2 ? 'opacity-100' : 'opacity-50'"
                            class="mx-auto block bg-white text-green-700 py-4 px-8 text-3xl rounded-[8px]"
                            @click="startGame"> Start
                        </button>
                        <p class="text-white text-center mt-2">
                            Minimum 2 players are required to start a game
                        </p>
                    </div>
                </div>
            </section>

        </main>


    </div>
</template>
<script setup>
const isGameStarted = ref(false);
// const playerNumbers = ref(0);
const players = ref([]);
const playerTurn = ref(0);
const gameTurn = ref(0);
const myScore = ref([0]);
const newPlayer = ref(null);
const isMaxPlayer = ref(false);


// const setNumberPlayers = (e) => {
//     playerNumbers.value = e.target.getAttribute('data-player-number');
//     console.log(playerNumbers.value)
// }

const nextTurn = () => {
    let actualPlayer = players.value[playerTurn.value].scores;
    actualPlayer.push(actualPlayer[actualPlayer.length - 1] + myScore.value);
    myScore.value = 0;
    if (playerTurn.value >= players.value.length - 1) {
        playerTurn.value = 0;
        gameTurn.value++
    } else {
        playerTurn.value++;
    }
}

const addPlayer = () => {
    if (players.value.length === 4) {
        isMaxPlayer.value = true
    } else if (newPlayer.value.value) {
        players.value.push({ name: newPlayer.value.value, scores: [0] });
        newPlayer.value.value = null;
    }
}

const removePlayer = (e) => {
    players.value.splice(e.target.getAttribute('data-player'), 1);
    if (isMaxPlayer.value) {
        isMaxPlayer.value = false
    }
}

const startGame = () => {
    if (players.value.length >= 2) {
        isGameStarted.value = true;
    }
}

const resetGame = () => {
    if (window.confirm('Are u sure u want to reset ?')) {
        isGameStarted.value = false;
        players.value = [];
        playerTurn.value = 0;
        gameTurn.value = 0;
        isMaxPlayer.value = false;
    }
}
</script>
<style>
html {
    @apply bg-green-700;
}

body {
    padding: 10px;
}

.container {
    max-width: 1440px;
    width: 100%;
    margin: 0 auto 0 auto;
}

td,
th {
    border: 1px solid;
    padding: 4px 24px;
}
</style>
  