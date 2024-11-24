<template>
    <div class="flex flex-col justify-center items-center p-10 h-screen w-screen bg-black text-white">
        <h1 v-if="currentQuestion"
            class="questionText text-7xl p-3">
            {{ currentQuestion }}
        </h1>

        <div class="choicesWrapper flex gap-5 p-5">
            <button v-for="(choice, key) in currentChoices"
                    :key="key"
                    @click="handleChoice(choice)">
                {{ choice.value }}
            </button>
        </div>
    </div>
</template>
<script setup>
import { ref } from 'vue';

// Import data object
import data from './assets/data.json';

// Reactive state for managing the current question and choices
const currentSetIndex = ref(1); // Track the current set number
const currentQuestion = ref(data[`set${currentSetIndex.value}`]?.question || '');
const currentChoices = ref(Object.values(data[`set${currentSetIndex.value}`]?.choices || {}));

// Function to handle choice selection
function handleChoice(choice) {
    if (choice.nestedSet) {
        // Navigate into the nested set
        currentQuestion.value = choice.nestedSet.question;
        currentChoices.value = Object.values(choice.nestedSet.choices);
    } else if (choice.endGame) {
        alert(choice.endGame); // Show the end message
        moveToNextSet(); // Move to the next set after endGame
    } else {
        moveToNextSet();
    }
}

// Function to move to the next question set
function moveToNextSet() {
    currentSetIndex.value += 1; // Increment the set index
    const nextSet = data[`set${currentSetIndex.value}`];

    if (nextSet && nextSet.question) {
        // Load the next set's question and choices
        currentQuestion.value = nextSet.question;
        currentChoices.value = Object.values(nextSet.choices || {});
    } else {
        alert('No more questions!'); // End of the game
        resetToInitial();
    }
}

// Reset to the initial question
function resetToInitial() {
    currentSetIndex.value = 1;
    currentQuestion.value = data[`set${currentSetIndex.value}`]?.question || '';
    currentChoices.value = Object.values(data[`set${currentSetIndex.value}`]?.choices || {});
}
</script>

<style>
button {
    font-size: 2rem;
    padding: 0.5rem;
    border: 2px solid white;
    font-family: 1rem;
    color: white;
    background-color: black;
}

button:hover {
    background-color: white;
    color: black;
}
</style>
