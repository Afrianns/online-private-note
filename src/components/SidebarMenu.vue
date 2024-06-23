<script setup lang="ts">
import { ref } from 'vue';
const data = defineProps(['title', 'content', 'date', 'idx']);

let open = ref(true);

function textLg() {
    if (data.title.length > 40) {
        return '...'
    }
}
function moreInfo() {
    open.value = !open.value;
}
</script>
<template>
    <div class="wrapper">
        <div class="header">
            <div class="title-wrapper">
                <p class="title">{{ data.title.slice(0, 40) }}{{ textLg() }}</p>
                <span class="sub-title">{{ data.date.toLocaleString('id-ID', {
                    dateStyle: 'long', timeStyle: 'short'
                }) }}</span>
            </div>
            <!-- <button @click="$emit('deleteData', data.title, $el)" class="detail">Delete</button> -->
            <!-- <button v-on:click="moreInfo()" class="detail">Detail</button> -->
        </div>
        <!-- <div class="body" :class="{ 'hidden': open }">
            {{ data.content }}
            <div class="action-wrapper">
                <button>Edit</button>
                <button @click="$emit('deleteData', data.title, $el)">Delete</button>
            </div>
        </div> -->
    </div>
</template>
<style>
.wrapper {
    background: var(--vt-c-divider-dark-1);
    padding: .6rem .5rem;
    line-height: 100%;
    cursor: pointer;
    /* margin: 1rem 0; */
}

.wrapper:hover {

    background: var(--color-border);
}

.header {
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
}


.title {
    color: rgb(209, 209, 209);
}

.sub-title {
    color: rgb(139, 139, 139);
    font-size: .5rem;
}

.body {
    border-top: 1px solid rgb(68, 68, 68);
    margin-top: 1rem;
    padding-top: 1rem;
}

.action-wrapper {
    display: flex;
    justify-content: space-between;
}

.detail {
    margin-left: 1rem;
}
</style>

<!-- 

let left = ref();
let right = ref();
let parent = ref();

let leftW = ref(100);
let rightW = ref(100);

const x = ref();

function ceks(param: any, current: any) {
  x.value = param.clientX;

  console.log(current, left.value);
  // console.log(param.clientX, param.clientY);

  // current.addEventListener("mousemove", function (a: any) {
  document.addEventListener('mousemove', handleMouseMove);

  function handleMouseMove(a: any) {
    console.log(a.offsetX, a.movementX, a);
    let currCoor = a.clientX - x.value;
    leftW.value = Math.round(a.movementX + leftW.value);
    // console.log(leftW.value, currCoor / 5);
  }

  document.addEventListener("mouseup", handleMouseUp)


  function handleMouseUp() {
    document.removeEventListener("mousemove", handleMouseMove);
    document.removeEventListener("mouseup", handleMouseUp);
  }
  // })
}

// function resize() {
//   console.log(left.value.style.height = left.value.scrollHeight + 'px');
// } -->
