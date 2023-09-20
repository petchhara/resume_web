<script setup>
import { onMounted, ref, watch, onUpdated } from 'vue';
import {
    app,
    auth,
    database,
    ref as refDb,
    set,
    push,
    onValue
} from '../firebaseConfig'
let chat = ref("");
let histories = ref([]);
let groups = ref([]);
let groupKey = ref("");
const bottomEl = ref(null);
const studentId = "รหัสนักศึกษา";
const db = refDb(database, 'all_chat/group_1/');
const dbGroup = refDb(database, 'all_chat/');

onValue(dbGroup, (snapshot) => {
    groups.value = snapshot.val();
})

const onSend = () => {
    if (chat.value != '') {
        push(refDb(database, `all_chat/${groupKey.value}`), {
            "user": studentId,
            "message": chat.value,
            "dateTime": new Date().toISOString()
        });
        chat.value = "";
    }
}
onValue(db, (snapshot) => {
    const data = snapshot.val();
    histories.value = data;
})
onUpdated(() => {
    bottomEl.value.scrollIntoView({ behavior: 'smooth' });
})
const selectGroup = (key) => {
    groupKey.value = key;
}
</script>

<template>
    <div class="flex gap-4">
        <div class="overflow-y-scroll bg-transparent h-[90vh] w-[30%]">
            <div 
                class="card card-side bg-base-100 shadow-xl w-full mb-4" 
                style="cursor: pointer;"
                v-for="(group, index) in groups" 
                :key="index" data-theme="cupcake"
                @click="selectGroup(index)">
                <div class="card-body">
                    <h2 class="card-title">{{ index }}</h2>
                    <p>{{ group[Object.keys(group)[Object.keys(group).length - 1]].message }}</p>
                </div>
            </div>
        </div>
        <div class="bg-transparent h-[90vh] w-[70%]">
            <div class="overflow-y-scroll h-[90%] p-5 card" data-theme="cupcake">
                <div v-for="(history, index) in groups[groupKey]"
                    :class="`chat ${history.user == studentId ? 'chat-end' : 'chat-start'}`" :key="index">
                    <div class="chat-header">
                        {{ history.user }}
                        <time class="text-xs opacity-50"> {{ history.dateTime }}</time>
                    </div>
                    <div class="chat-bubble" v-if="history.message != ''">{{ history.message }}</div>
                </div>
                <div ref="bottomEl"></div>
            </div>
            <div class="flex h-[10%] gap-4 bg-transparent pt-4" data-theme="cupcake">
                <input v-on:keyup.enter="onSend" v-model="chat" type="text" placeholder="Type here"
                    class="input input-bordered w-[80%] h-full" />
                <button @click="onSend" class="w-[20%] btn h-full">Send</button>
            </div>
        </div>
    </div>
</template>
<style scoped></style>