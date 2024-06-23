<script setup lang="ts">
import Content from "./components/Content.vue";
import SidebarMenu from "./components/SidebarMenu.vue";
import { ref } from 'vue';


let db: any = ref();
let request = indexedDB.open('notes');
let items: any = ref([]);
let panel = ref(true);
let isFileOpen = ref(false);

let title = ref('');
let body = ref('');

let content = ref({
  title: '',
  body: '',
  created_at: '',
});

let error = ref('');
let empty = ref('');
let notSelected = ref("There isn't File Selected.");
let formEmpty = ref('');

let isDelete = ref(false);

request.onsuccess = () => {
  db = request.result;

  listDB();
}

request.onupgradeneeded = function () {
  if (!db.objectStoreNames.contains('notes')) { // if there's no "notes" store
    db.createObjectStore('notes', { autoIncrement: true }); // create it
  }
};


function createData() {

  if (!title.value) {
    formEmpty.value = "Don't submit empty ideas!";
    return;
  }
  let transaction = db.transaction("notes", "readwrite"); // (1)

  // // get an object store to operate on it
  let notes = transaction.objectStore("notes"); // (2)

  let note = {
    title: title.value,
    body: body.value,
    created: new Date()
  };
  let request = notes.add(note);

  request.onsuccess = function () {
    formEmpty.value = '';
    error.value = '';
    title.value = '';
    body.value = '';
    // console.log('success', request);
    createPanel();
    listDB();
  }

  request.onerror = function () {
    if (request.error.name == 'ConstraintError') {
      error.value = 'Title Already Exist!';
    } else {
      error.value = request.error.message;
    }
  }

}


async function listDB() {
  let transaction = db.transaction("notes");
  let request = transaction.objectStore('notes').getAll();

  request.onsuccess = (e: any) => {
    if (request.result.length > 0) {
      empty.value = '';
      formEmpty.value = '';
      items.value = request.result;
    } else {
      console.log('wow');
      empty.value = 'There no File.';
    }
  }
  request.onserror = (e: any) => {
    console.log(request);
  }
}

function createPanel() {
  formEmpty.value = '';
  error.value = '';
  panel.value = !panel.value;
}


function deleteData(key: string, event: any) {
  console.log(key);

  let res = items.value.findIndex(item => item.title == key);
  items.value.splice(res, 1);

  let transaction = db.transaction("notes", 'readwrite');
  let objectStore = transaction.objectStore('notes')
  let request = objectStore.delete(key);

  request.onsuccess = () => {
    // isDelete.value = true;
    notSelected.value = "There isn't File Selected.";
    if (items.value.length >= 1) {
      loadContent(items.value[0]?.title)
    } else {
      empty.value = "There aren't Files.";
    }
  }
  request.onerror = () => {
    console.log(request);

  }
}


function loadContent(param: string = 'empty') {
  isFileOpen.value = true;
  // isDelete.value = false;

  console.log(content.value.title, param);
  items.value.forEach((element: any) => {
    if (content.value.title == element.title) {
      element.body = content.value.body;
    }
  });

  items.value.forEach((element: any) => {
    if (element.title == param) {
      content.value.title = element.title
      content.value.body = element.body
      content.value.created_at = element.created
    }
  });
  notSelected.value = '';
}

function saveData(param: string) {

  items.value.forEach(element => {

    if (element.title == param) {
      let transaction = db.transaction("notes", 'readwrite');
      let objectStore = transaction.objectStore('notes');
      let request = objectStore.get(param);

      request.onsuccess = () => {

        let updateData = request.result;

        updateData.body = content.value.body
        let updateRequest = objectStore.put(updateData);

        updateRequest.onsuccess = () => {
          console.log(items.value);
        }
      }

    }
  });
  console.log(param);
}

</script>

<template>

  <div :class="{ 'hidden': panel, 'backdrop': !panel }">
    <div class="form-wrapper">
      <div class="form-header">
        <h3>CREATE NEW NOTE</h3>
        <button v-on:click="createPanel()">Close</button>
      </div>
      <input type="text" v-model="title" placeholder="fill the title">
      <button v-on:click="createData()" class="new-note-btn">Add New</button>
      <p v-show="error" class="error">{{ error }}</p>
      <p v-show="formEmpty" class="error">{{ formEmpty }}</p>
    </div>
  </div>

  <header>
    <div class="sidebar">
      <h1>Private Note.</h1>
      <button v-on:click="createPanel()">+</button>
    </div>
    <ul class="files-wrapper" v-if="!empty">
      <li v-for="(item, index) in items" :key="index">
        <SidebarMenu v-on:click="loadContent(item.title)" :title="item.title" :date="item.created"
          :content="item.content" :idx="index" class="content-wrapper" />
      </li>
    </ul>
    <p v-else class="empty-sidebar">
      {{ empty }}
    </p>
  </header>
  <div class="container">
    <main>
      <div class="content">
        <Content :content="content" v-on:delete-data="deleteData" :empty="notSelected" v-on:save-data="saveData" />
      </div>
    </main>
  </div>
</template>

<style scoped>
.logo {
  display: block;
  margin: 0 auto 2rem;
}

.new-note-btn {
  margin: 1rem 0 0;
  padding: .5rem 0;
  border: 1px solid orange;
  background-color: orangered;
  color: aliceblue;
}

.empty-sidebar {
  margin: 2rem 0;
  text-align: center;
  color: gray;
  font-size: .75rem;
}

header {
  background-color: rgb(56, 56, 56);
  padding: 2rem 1rem;
  height: 100vh;
  width: 24rem;
}

.sidebar {
  font-size: .6rem;
  display: flex;
  place-items: center;
  max-width: 900px;
  margin: auto;
  justify-content: space-between;
}

.files-wrapper {
  list-style: none;
  padding: .6rem 0;
}

.files-wrapper>* {
  margin: .2rem 0;
}

.error {
  color: red;
  padding: .5rem 0;
}

.logo {
  margin: 0 2rem 0 0;
}

header .wrapper {
  display: flex;
  place-items: flex-start;
  flex-wrap: wrap;
}

.form-header {
  margin-bottom: 1rem;
  justify-content: space-between;
  align-items: center;
  display: flex;
}

main {
  padding: 2rem;
  margin: 0 auto;
  font-size: .6rem;
  width: 100%;
  height: 100vh;
  max-width: 1100px;
}

.container {
  width: 100vw;
  overflow-y: auto;
}

.backdrop {
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  background-color: rgba(46, 53, 48, .5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.form-wrapper {
  display: flex;
  flex-direction: column;
  background-color: var(--color-background);
  padding: 1.5rem 1rem;
  width: 40vw;
  height: fit-content;
}
</style>
