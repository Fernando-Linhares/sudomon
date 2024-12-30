
<script setup>
import {ref, reactive, defineProps} from 'vue';
import logo from './assets/logo.png';
import pokeball from './assets/pokeball.png';
import pikachu from './assets/pikachu.png';
import red from './assets/red.png';
import blue from './assets/blue.png';
import genz from './assets/genz.png';
import female from './assets/female.png';

const props = defineProps({
    register: Function
});

const data = reactive({
    avatars: [
        {
            name: 'red',
            src: red,
            selected: true
        },
        {
            name: 'blue',
            src: blue,
            selected: false
        },
        {
            name: 'genz',
            src: genz,
            selected: false
        },
        {
            name: 'female',
            src: female,
            selected: false
        }
    ],
    nickName: ''
})

const startGame = () => {
    let user = {avatar: {src: null, name: null}};
    user.name = data.nickName;
    user.avatar.src = data.avatars.find(a => a.selected).src;
    user.avatar.name = data.avatars.find(a => a.selected).name;
    props.register(user);
};

const ballMove = ref({});
const openForm = ref({});

let flag = false;
openForm.value = false;
setInterval(()=>{
    if(flag) {
        ballMove.value = 'mt-0';
    }else {
        ballMove.value = 'mt-4'
    }
    flag = !flag;
}, 400);

const select = avatar => {
    data.avatars.forEach(a => a.selected=false);
    avatar.selected = true;
}

const openPlayGame = () => {
    openForm.value = true;
    window.document.getElementById('audio').play()
}

</script>

<template>
    <div
    class="min-h-screen bg-gray-900/80 flex items-center justify-center fixed top-0 left-0 bottom-0 right-0 z-50 ">
      <div 
        class="absolute inset-0 bg-gray-900 bg-opacity-80"
      ></div>
      <div class="relative w-4/5 max-w-lg bg-white rounded-lg shadow-lg p-6">
        <div class="flex justify-between items-center">
          <h2 class="text-2xl font-bold text-gray-800" >{{ openForm ? 'Cadastre-se' : 'Bem-vindo ao Sudomon!'}}</h2>
        </div>
        <div v-if="openForm">
            <div>Avatar</div>
            <div class="flex justify-center">
                <div class="my-4 flex items-center space-x-4 w-4/5 overflow-x-hidden hover:overflow-x-scroll
                max-sm:grid max-sm:grid-cols-2 max-sm:space-x-0
                
                ">
                    <img v-for="avatar in data.avatars" :src="avatar.src" :alt="avatar.name" :key="avatar.name"
                        class=" cursor-pointer size-40 p-4 rounded hover:bg-slate-300"
                        :class="{'bg-slate-300': avatar.selected}"
                        @click="select(avatar)"
                    >
                </div>
            </div>
            <div class="flex justify-center space-x-4 items-center max-sm:grid max-sm:space-x-0">
                <div class="text-slate-700">Nick Name</div>
                <input v-model="data.nickName" type="text" class="outline-sky-800 border-slate-600 border rounded p-1">
            </div>
        </div>
        <div class="my-4 text-gray-600" v-else>
          <p>
            Combine a diversão do Sudoku com a aventura dos Pokémon! Resolva
            desafios estratégicos como um grande mestre pokemon
          </p>
          <div class="flex justify-center">
            <img :src="pikachu" alt="" class="size-40">
          </div>
          <div class="flex items-center">
              <img 
                :src="logo" 
                alt="Sudomon"
                class="mt-4 w-2/3 rounded-lg"
              />
              <img :src="pokeball" alt="" class="size-20 transition-all delay-300"
                :class="ballMove"
              >
          </div>
        </div>
        
        <!-- Footer -->
        <div class="flex justify-center space-x-4 mt-4" >
            <button v-if="openForm"
            class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-lg"
            @click="startGame()"
          >
            Confirmar
          </button> 
          <button v-else
            class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-lg"
            @click="openPlayGame()"
          >
            Jogar Agora
          </button>
        </div>
      </div>
    </div>
  </template>
  