<script setup>
import { reactive, defineProps } from 'vue';
import red from './assets/red.png';
import blue from './assets/blue.png';
import genz from './assets/genz.png';
import female from './assets/female.png';

const props = defineProps({
  goBack: Function,
  ranking: Array,
});

const ordenateByScore = players => {
  return players.sort((a, b) => b.score - a.score );
};

const data = reactive({
  avatars: [
    { name: 'red', src: red },
    { name: 'blue', src: blue },
    { name: 'genz', src: genz },
    { name: 'female', src: female },
  ],
});
</script>

<template>
  <div
    class="min-h-screen bg-gray-900/80 flex items-center justify-center fixed top-0 left-0 bottom-0 right-0 z-50"
  >
    <div class="absolute inset-0 bg-gray-900 bg-opacity-80"></div>
    <div class="relative w-4/5 max-w-lg bg-white rounded-lg shadow-lg p-6">
      <div class="flex justify-between items-center">
        <h2 class="text-2xl font-bold text-gray-800">
          Ranking dos melhores treinadores Pokémon!
        </h2>
      </div>

      <div class="my-4 text-gray-600">
        <ol class="space-y-4">
          <li
            v-for="(trainer, index) in ordenateByScore(ranking)"
            :key="index"
            class="flex items-center space-x-4 bg-gray-100 p-3 rounded-lg shadow"
          >
            <img
              :src="data.avatars.find((avatar) => avatar.name === trainer.avatar)?.src"
              alt="Avatar"
              class="w-12 h-12 rounded-full"
            />
            <div>
              <h3 class="text-lg font-semibold text-gray-800">
                {{ trainer.name }}
              </h3>
              <p class="text-sm text-gray-600">
                Rank: {{ index + 1 }} | Pontos: {{ trainer.score }}
              </p>
            </div>
          </li>
        </ol>
      </div>

      <!-- Footer -->
      <div class="flex justify-center space-x-4 mt-4">
        <button
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-lg"
          @click="props.goBack()"
        >
          Voltar
        </button>
      </div>
    </div>
  </div>
</template>
