<script setup>
  import { onMounted, reactive } from 'vue';
  import Swal from 'sweetalert2';
  import pokeball from './assets/pokeball.png';
  import gengar from './assets/gengar.png';
  import pikachu from './assets/pikachu.png';
  import charmander from './assets/charmander.png';
  import bulbasaur from './assets/bulbasaur.png';
  import squirtle from './assets/squirtle.png';
  import togepi from './assets/togepi.png';
  import mewtwo from './assets/mewtwo.png';
  import articuno from './assets/articuno.png';
  import abra from './assets/abra.png';
  import life from './assets/life.png';
  import GameModal from './GameModal.vue';
  import RankingModal from './RankingModal.vue';
  import soundtrack from './assets/pokemon_theme.mp3';

  const data = reactive({
    cells: [],
    trys: 4,
    playaudio: false,
    user: {name: null , avatar: { src:null, name: null }},
    showModal: true,
    showRanking: false,
    timer: 0 ,
    score: 0,
    players: []
  });

  const isBord = (index) => {
    let list = [];
    let c = 8;
    for(let i =0; i< 9; i++) {
      list.push(c);
      c += 9;
    }

    return list.find(n => n == index);
  }

  function isValid(board, row, col, num) {
      for (let i = 0; i < 9; i++) {
          if (board[row][i] === num || board[i][col] === num) {
              return false;
          }

          const startRow = Math.floor(row / 3) * 3;
          const startCol = Math.floor(col / 3) * 3;
          const boxRow = startRow + Math.floor(i / 3);
          const boxCol = startCol + (i % 3);
          if (board[boxRow][boxCol] === num) {
              return false;
          }
      }
      return true;
  }

  function fillSudoku(board) {
      for (let row = 0; row < 9; row++) {
          for (let col = 0; col < 9; col++) {
              if (board[row][col] === 0) {
                  const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9].sort(() => Math.random() - 0.5);
                  for (let num of numbers) {
                      if (isValid(board, row, col, num)) {
                          board[row][col] = num;

                          if (fillSudoku(board)) {
                              return true;
                          }

                          board[row][col] = 0;
                      }
                  }
                  return false; 
              }
          }
      }
      return true;
  }

  function generateSudokuMatrix() {
      const board = Array.from({ length: 9 }, () => Array(9).fill(0));
      fillSudoku(board);
      return board;
  }

  const close = () => data.cells.forEach(c => c.toggleDropdown = false);
  const closeOut = (event) => {
    if (event.target.classList.contains('out-side')) {
      close()
      data.cells.forEach(cell => cell.focused = false);
    }
  };

  const dropdown = (event, cell) => {
    close();
    if (event.target.classList.contains('cell')) {
      cell.toggleDropdown = true;
    }
  }

  const setVal = (cell, num) => {
    if(num == cell.valid) {
      let timeVariant = (10 * Math.floor(data.timer / 60)) + (800 * (4 - data.trys));
      timeVariant  = timeVariant > 6000 ? 6000 : timeVariant;
      data.score += (7000 - timeVariant);
      cell.success = true;
      cell.error = false;

      if (data.cells.filter(cell => cell.selected).length == (data.cells.length - 1)) {
        Swal.fire({
          title: 'You Win',
          text: 'Parabéns você provou ser um grande treinador pokemon!',
          showCancelButton: true,
          showConfirmButton: true,
          icon: 'success'
        }).then(() => {
          if(!localStorage?.ranking) {
            localStorage.ranking = JSON.stringify([
              {name: data.user.name, avatar: data.user.avatar.name, score: data.score }
            ]); 
          }else {
            let json = JSON.parse(localStorage.ranking);
            json.push({name: data.user.name, avatar: data.user.avatar.name, score: data.score });
            localStorage.ranking = JSON.stringify(json);
          }

          data.players.push({name: data.user.name, avatar: data.user.avatar.name, score: data.score });
          window.location = window.location.href;
        });
      }
    } else {
      data.trys--
      if(data.trys == 0) {
        Swal.fire({
          title: 'You Loose',
          text: 'Suas vidas acabaram deseja tentar denovo?',
          showCancelButton: true,
          showConfirmButton: true,
          icon: 'error'
        }).then(() => {
          window.location = window.location.href;
        })
      }else {
        Swal.fire('Erro', 'Você possui ' + data.trys + ' vida(s).', 'error')
      }

      cell.error = true;
    }

    cell.num = num;
    cell.selected = true;
    data.cells.forEach(cell => cell.focused = false);

    close();
  }

  const nums = {
    1: { src: gengar, name: 'gengar' },
    2: { src: pikachu, name: 'pikachu' },
    3: { src: charmander, name: 'charmander' },
    4: { src: bulbasaur, name: 'bulbasaur' },
    5: { src: squirtle, name: 'squirtle' },
    6: { src: togepi, name: 'togepi' },
    7: { src: mewtwo, name: 'mewtwo' },
    8: { src: articuno, name: 'articuno' },
    9: { src: abra, name: 'abra' }
  };

  onMounted(() => {    
    const sudokuMatrix = generateSudokuMatrix();
    sudokuMatrix.forEach((row) => {
        let randNums = [
          Math.floor(Math.random() * (9 - 1 + 1) + 1),
          Math.floor(Math.random() * (9 - 1 + 1) + 1),
          Math.floor(Math.random() * (3 - 0 + 0) + 0),
          Math.floor(Math.random() * (3 - 0 + 0) + 0),
          Math.floor(Math.random() * (3 - 0 + 0) + 0),
          Math.floor(Math.random() * (10 - 1 + 1) + 1),
          Math.floor(Math.random() * (10 - 1 + 1) + 1),
          Math.floor(Math.random() * (10 - 1 + 1) + 1),
          Math.floor(Math.random() * (9 - 1 + 1) + 1),
          Math.floor(Math.random() * (9 - 1 + 1) + 1),
        ];


        row.forEach((item, i) => {
          if(randNums.find(n => i == n)) {
            
            data.cells.push({
              num: item,
              src: nums[item],
              valid: item,
              toggleDropdown: false,
              selected: true,
              error: false,
              success: false
            });
          } else {
            data.cells.push({
                num: item,
                valid: item,
                src: pokeball,
                toggleDropdown: false,
                selected: false,
                error: false,
                success: false,
                focused: false
              })
          }
        });
    });

    if (localStorage?.ranking) {
      data.players = JSON.parse(localStorage.ranking);
    }
  });

const register = user => {
  data.user.name = user.name;
  data.user.avatar.src = user.avatar.src;
  data.user.avatar.name = user.avatar.name;
  data.showModal = false;

  setInterval(()=> data.timer++, 1000);
};

  const focus = item => {
    data
      .cells
      .forEach(cell => cell.focused = false);

    data
      .cells
      .filter(cell => cell.selected && cell.num == item)
      .forEach(cell => cell.focused = true);
  };

  const reset = item => {
      item.selected = false;
      item.error = false;
      item.success = false;
  }

  const formatTimer = timer => {
    if (timer < 60) {
      return  timer < 10 ? '00:00:0'+timer :'00:00:'+timer ;
    }

    if(timer < 60 * 60) {
      let mins = Math.floor(timer / 60);
      let fmins = mins < 10 ? '0' + mins : mins;
      let sec = timer - Math.floor(mins * 60);
      let fsec = sec < 10 ? '0' + sec: sec;
      return  `00:${fmins}:${fsec}`;
    }

    let hours = Math.floor(Math.floor(timer / 60) / 60);
    let fhours = hours < 10 ? '0'+hours : hours;
    let mins =  Math.floor((timer / 60) - (hours * 60));
    let fmins = mins < 10 ? '0' + mins: mins;
    let sec = timer - (hours * 60 * 60) - (mins * 60);
    let fsec = sec < 10 ? '0' + sec: sec;

    return  `${fhours}:${fmins}:${fsec}`
  }


</script>
<template>
    <div class="flex justify-center absolute top-10 w-full space-x-4">
      <div class="bg-sky-800  text-white p-4 rounded-lg">
        <div>Timer: {{ formatTimer(data.timer) }}</div>
        <div>Score: {{ data.score }}</div>
      </div> 
      <div @click="data.showRanking = true" class="bg-red-700 hover:bg-red-600 space-x-2 text-white p-4 rounded-lg flex cursor-pointer items-center">
        <div class="text-lg">Ranking</div>
        <i class="material-icons">
          star
        </i>
      </div>
    </div>
  
  <RankingModal :goBack="() => data.showRanking = false" :ranking="data.players" v-if="data.showRanking"/>
  <GameModal :register="register" v-if="data.showModal" />
  <div class="fixed left-0 top-2 p-4 rounded-r-lg bg-indigo-600" v-if="data.user.name">
      <div class="flex space-x-4 items-center">
        <div>
          <img :src="data.user.avatar.src" :alt="data.user.name" class="size-16">
        </div>
        <div>
          <div>
            <span class="text-lg text-slate-100">
              {{ data.user.name  }}
            </span>
          </div>
          <div class="flex justify-center space-x-1">
            <img v-for="i of data.trys" :key="i" :src="life" alt="life" class="size-4">
          </div>
        </div>
      </div>
  </div>

  <div style="background-image: url('background.png');; background-size: 160%;"
     class="out-side lg:min-h-screen flex items-center justify-center bg-gray-100
     max-sm:py-10
     " @click="closeOut($event)">
     
    <div class="bg-slate-400 rounded p-2 grid grid-cols-9 max-sm:w-full max-sm:h-[500px]">
      <div v-for="(cell, i) in data.cells" :key="cell.num" 
       class="p-4 rouded border flex justify-center cursor-pointer cell max-sm:p-1"
       :class="{
        'border-b-8' : Math.floor(i / 9) == 2
        ||  Math.floor(i/ 9) == 5,
        'maxsm:border-b-4' : Math.floor(i / 9) == 2
        ||  Math.floor(i/ 9) == 5,
        'border-r-8':  ((i+ 1) % 3) == 0 && !(isBord(i)),
        'max-sm:border-r-4':  ((i+ 1) % 3) == 0,

        'bg-red-600': cell.error,
        'bg-green-600': cell.success,
        'bg-indigo-700': cell.focused
      }"
      @click="dropdown($event, cell)"
      >
      <div>
        <span class="cell" v-if="!cell.selected">
          <span></span>
          <img :src="pokeball" alt="pokeball" class="size-12 max-sm:size-10 cell border-none" >
        </span>
        <span class="cell flex space-between border border-slate-400" v-else>
          <span>
            {{ cell.num }}
          </span>
          <img :src="nums[cell.num].src" :alt="nums[cell.num].name"
          class="size-12  cell p-1 rounded max-sm:size-10"
          >
        </span>
        <div :class="{'hidden': !cell.toggleDropdown, 'max-sm:hidden': !cell.toggleDropdown}"
          class="absolute rounded bg-slate-300 flex max-sm:grid z-20 max-sm:fixed max-sm:top-40 max-sm:left-40 max-sm:right-20 max-sm:rounde-lg"
        >
          <div class="hover:bg-slate-200 px-6 py-2 max-sm:p-3 max-sm:text-center" @click="reset(cell)">
            -
          </div>
          <div
            v-for="item in 9" :key="item" class="hover:bg-slate-200 px-6 py-2 max-sm:text-center"
            @click="setVal(cell, item)"
            >
              {{ item }}
          </div>
        </div>
      </div>
    </div>
    </div>
  </div>
  <div class="fixed right-0 lg:top-7 bg-slate-100 rounded-tl-lg rounded-bl-lg divide-y divide-slate-200
  max-sm:bottom-0 max-sm:left-0 max-sm:flex max-sm:w-full max-sm:px-2
  ">
    <div v-for="(item, index) in 9" :key="index"
      class="p-4 px-6 max-sm:px-2 max-sm:pt-2 rounded-tl-lg rounded-bl-lg  cursor-pointer hover:bg-slate-200"
      :class="{'hidden': data.cells.filter(cell => !cell.selected && cell.valid == item).length == 0}"
      >
      <div @click="focus(item)" class="flex items-center space-x-4 max-sm:space-x-0 max-sm:grid">
        <span class="rounded-full bg-orange-600 px-2 p-1 text-white max-sm:text-xs max-sm-">
          {{ data.cells.filter(cell => !cell.selected && cell.valid == item).length }}
        </span>
        <span class="text-2xl max-sm:text-sm">{{ item }}</span>
        <img :src="nums[item].src" :alt="nums[item].name" class="size-10">
      </div>
    </div>
  </div>
  <audio id="audio">
      <source :src="soundtrack" type="audio/mp3">
      Seu navegador não possui suporte ao elemento audio
  </audio>
</template>