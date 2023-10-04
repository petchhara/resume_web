<script setup>
import { onMounted, ref, watch, onUpdated } from 'vue';
import {
    app,
    auth,
    database,
    ref as refDb,
    set,
    push,
    onValue,
    remove,
    child,
    get
} from '../firebaseConfig';
let chat = ref("");
let histories = ref([]);
let historyKey = ref(""); // collect select group
const bottomEl = ref(null);
const studentId = "รหัสนักศึกษา";
const db = refDb(database, 'all_chat/');

onValue(db, (snapshot) => {
    histories.value = snapshot.val() ?? [];
})

const onSend = () => {
    if (chat.value != '' && historyKey.value != '') {
        push(refDb(database, `all_chat/${historyKey.value}`), {
            "user": studentId,
            "message": chat.value,
            "dateTime": new Date().toISOString()
        });
        chat.value = "";
    }
}
onUpdated(() => {
    bottomEl.value.scrollIntoView({ behavior: 'smooth' });
})
const selectGroup = (key) => {
    historyKey.value = key;
}
let groupChatName = ref("");
const createGroup = () => {
    if (groupChatName.value != '') {
        get(child(db, `${groupChatName.value}`)).then((snapshot) => {
            if (snapshot.exists()) {
                alert("Cannot create chat because chat is exists.")
            } else {
                push(refDb(database, `all_chat/${groupChatName.value}`), {
                    "user": studentId,
                    "message": '',
                    "dateTime": new Date().toISOString()
                });
            }
            groupChatName.value = '';
        }).catch((err) => {
            console.error(err);
        })

    }

}
const deleteGroupChat = (key) => {
    remove(refDb(database, `all_chat/${key}`));
    groupChatName.value = '';
}
</script>

<template>
    <div class="flex gap-4">
        <div class="bg-transparent h-[90vh] w-[30%]">
            <div class="overflow-y-scroll w-full h-[90%]">

                <div class="card card-side bg-base-100 shadow-xl w-full mb-4" style="cursor: pointer;"
                    v-for="(group, index) in histories" :key="index" data-theme="cupcake" @click="selectGroup(index)">
                    <div class="card-body">
                        <h2 class="card-title">
                            <svg @click="deleteGroupChat(index)" xmlns="http://www.w3.org/2000/svg" fill="none"
                                viewBox="0 0 24 24" stroke-width="1.5" stroke="#FF5859" class="w-6 h-6">
                                <path stroke-linecap="round" stroke-linejoin="round"
                                    d="M14.74 9l-.346 9m-4.788 0L9.26 9m9.968-3.21c.342.052.682.107 1.022.166m-1.022-.165L18.16 19.673a2.25 2.25 0 01-2.244 2.077H8.084a2.25 2.25 0 01-2.244-2.077L4.772 5.79m14.456 0a48.108 48.108 0 00-3.478-.397m-12 .562c.34-.059.68-.114 1.022-.165m0 0a48.11 48.11 0 013.478-.397m7.5 0v-.916c0-1.18-.91-2.164-2.09-2.201a51.964 51.964 0 00-3.32 0c-1.18.037-2.09 1.022-2.09 2.201v.916m7.5 0a48.667 48.667 0 00-7.5 0" />
                            </svg>
                            {{ index }}
                        </h2>
                        <p>{{ group ? group[Object.keys(group)[Object.keys(group).length - 1]]?.message : "" }}</p>
                    </div>
                </div>
            </div>
            <div class="w-full h-[10%] pt-4">
                <button class="btn w-full h-full" data-theme="cupcake" onclick="my_modal_1.showModal()">Add Group</button>
                <dialog id="my_modal_1" class="modal">
                    <div class="modal-box">
                        <div class="form-control w-full">
                            <label class="label">
                                <span class="label-text">Group Name</span>
                            </label>
                            <input type="text" placeholder="Type here" class="input input-bordered w-full"
                                v-model="groupChatName" />
                        </div>
                        <div class="modal-action">
                            <form method="dialog">
                                <!-- if there is a button in form, it will close the modal -->
                                <button class="btn" @click="createGroup">Save</button>
                            </form>
                        </div>
                    </div>
                </dialog>
            </div>

        </div>
        <div class="bg-transparent h-[90vh] w-[70%]">
            <div class="overflow-y-scroll h-[90%] p-5 card" data-theme="cupcake">

                <div v-for="(history, index) in histories[historyKey]"
                    :class="`chat ${history.user == studentId ? 'chat-end' : 'chat-start'}`" :key="index">
                    <div class="chat-header">
                        {{ history.user }}
                        <time class="text-xs opacity-50"> {{ history.dateTime }}</time>
                    </div>
                    <div class="chat-bubble" style="word-break: break-word;">{{ history.message }}</div>
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