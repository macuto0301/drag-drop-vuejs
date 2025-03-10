<script setup>
import { reactive } from 'vue';
import InputNew from './InputNew.vue';

let boards = reactive([
  {
    id: crypto.randomUUID() ,   
    name: 'Tablero 1',          
    items: [
      { id: crypto.randomUUID(), title: 'Feature de archivos', completed: false },
      { id: crypto.randomUUID(), title: 'Resolver Bug', completed: true },
    ],
    },
    {
      id: crypto.randomUUID(),
      name: 'Tablero 2',
      items: [
        { id: crypto.randomUUID(), title: 'Mandar Reporte', completed: false },
        { id: crypto.randomUUID(), title: 'Code Review', completed: false },
      ],
    },
  ]);
function hanleNewItem(text, board) {
    console.log(text.value,board.id,board.name);
    board.items.push({
        id: crypto.randomUUID(),
        title: text.value,
        completed: false,
    });
    
}
function hanleNewBoard() {
    const name = prompt('Nombre del tablero');
    if (!!name) {
        boards.push({
            id: crypto.randomUUID(),
            name: name,
            items: [],
        });
    }
}

function startDrag(evt,board,item) {
    if (!evt.dataTransfer) return;
    evt.dataTransfer.setData(
        'text/plain',
        JSON.stringify({ boardId: board.id, itemId: item.id })
    );
}

function onDrop(evt,dest) {
    const { boardId, itemId } = JSON.parse(
        evt.dataTransfer.getData('text/plain')
    );

    
    const originBoard = boards.find((item) => item.id === boardId);
    const OriginItem = originBoard.items.find((item) => item.id === itemId);
   
    dest.items.push({...OriginItem});
    originBoard.items = originBoard.items.filter((item) => item.id !== itemId);
}


</script>

<template>
<nav>
    <ul>
        <li><a href="#" @click.prevent="hanleNewBoard">Create boards</a></li>
    </ul>
</nav>
<div class="board-container">
    <div class="boards">
        <div 
        class="board" 
        @drop="onDrop($event,board)" 
        @dragover.prevent 
        @dragenter.prevent 
        v-for="board in boards" 
        :key="board.id"
        >
            <h2>{{ board.name }}</h2>
            <InputNew @on-new-item="(text) => hanleNewItem(text, board)"/>
            <ul class="items">
                <li 
                class="item"
                draggable="true" 
                @dragstart="startDrag($event, board, item)"
                v-for="item in board.items" 
                :key="item.id">
                <input type="checkbox" :checked="item.completed" />

                    <span>{{ item.title }}</span>
                </li>
            </ul>
        </div>
    </div>
</div>


</template>

<style scoped>
nav {
    background-color: #000000;
    margin-bottom: 10px;
    
}
nav ul {
    list-style: none;
    padding: 0px;
    margin: 0px;
    display: flex;
}
nav ul li a{
    display: block; 
    padding:10px;
   
    color: #fff;
    text-decoration: none;
}
.boards {
    display: flex;
    gap: 10px;
}
.board {
    border: 1px solid #000000;
    background-color: rgb(218, 213, 213);
    padding: 10px;
    border-radius: 5px;
    width: 300px;
}
.items {
    display: flex;
    flex-direction: column ;
    gap: 5px;
    list-style: none;
    padding: 10px;
}
.item {
    background-color: #ffffff;
    display: flex;
    
    align-items: center;
    gap: 10px;
}

</style>