<script setup lang="ts">
import IconFiles from "./icons/IconFiles.vue";
import IconCheck from "./icons/IconCheck.vue";
// import Swal from 'sweetalert2/dist/sweetalert2';

import { marked } from 'marked';
import { ref, computed } from "vue";

const emit = defineEmits(['deleteData', 'saveData']);
const data = defineProps(['content', 'empty']);

let txtStatusPreview = ref('preview')
let isPrev = ref(false);
let txtMarked = computed(() => marked(data.content.body));
let txtStatusSave = ref('Save');


function markdown() {
    isPrev.value = !isPrev.value;
    txtStatusPreview.value = (isPrev.value) ? "previewing" : "preview";
}

function getClicked(param: string) {
    txtStatusSave.value = (param == 'Saved') ? param : 'Save';
    setTimeout(() => {
        txtStatusSave.value = 'Save';
    }, 1500);
}

function confirm() {
    Swal.fire({
        title: "Are you sure?",
        text: "You won't be able to revert this!",
        showCancelButton: true,
        confirmButtonText: "Delete!",
        cancelButtonText: "Cancel!",
        reverseButtons: true,
        color: 'rgb(236, 236, 236)',
        background: '#282828',
        confirmButtonColor: 'rgb(245, 51, 51)',
    }).then((result: any) => {
        if (result.isConfirmed) {
            emit('deleteData', data.content.title);
        }
    });
}

</script>
<template>
    <div v-if="!data.empty">
        <div class="content-header">
            <h1>{{ data.content.title }}</h1>
            <p>{{ data.content.created_at }}</p>
        </div>
        <div class="editor">
            <div class="preview">
                <button v-on:click="markdown">{{ txtStatusPreview }}</button>
            </div>
            <div class="prev-marked" :class="{ 'hidden': !isPrev }" v-html="txtMarked"></div>
            <textarea name="" id="" :class="{ 'hidden': isPrev }" v-model="data.content.body">
            </textarea>
            <div class="action-wrapper">
                <div class="save-btn">
                    <button @click="$emit('saveData', data.content.title); getClicked('Saved')">
                        {{ txtStatusSave }}
                    </button>
                    <IconCheck v-if="txtStatusSave == 'Saved'" />
                </div>
                <button @click="confirm">Delete</button>
            </div>
        </div>
    </div>
    <div v-else class="empty-wrapper">
        <IconFiles :empty="empty" />
    </div>
</template>
<style>
.hidden {
    display: none;
}

.save-btn {
    display: flex;
    align-items: center;
    gap: 5px;
    color: rgb(236, 236, 236);
}


.prev-marked {
    color: rgb(226, 226, 226);
    font-size: .75rem;
    line-height: 2;
    padding: 15px 10px;
    background-color: var(--color-border);
}

.editor {
    width: 100%;
    /* padding: 2px; */
    background-color: rgb(35, 35, 35);
    /* padding-top: 2rem; */
}

button {
    margin: 5px;
}

.preview {
    text-align: right;

}

.action-wrapper button {
    padding: 5px 20px;
}

.action-wrapper button:nth-child(2) {
    background: rgb(238, 19, 19);
    color: white;
    border: red;
}


.empty-wrapper {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 90vh;
}

.content-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin: 1rem 0;
}

input,
textarea {
    color: white;
    border: 1px solid rgb(85, 85, 85);
    background-color: var(--color-border);
    padding: .5rem;
}

textarea {
    resize: none;
    width: 100%;
    outline: none;
    height: 70vmin;
    min-height: 50px;
}
</style>