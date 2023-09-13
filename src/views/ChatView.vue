<script setup>
import { onMounted, ref } from 'vue';
import {
    app,
    auth,
    database,
    ref as refDb,
    set,
    push
} from '../firebaseConfig'
let chat = ref("");
const studentId = "รหัสนักศึกษา";
const onSend = () => {
    push(refDb(database,'all_chat/group_1/'), {
        "user": studentId,
        "message": chat.value,
        "dateTime": new Date().toISOString()
    });
    chat.value = "";
}
</script>

<template>
    <div class="flex " data-theme="cupcake">
        <div class="overflow-y-scroll bg-white h-[90vh] w-[30%]">
            <div class="w-full bg-[#DD85E6] h-36 border-cyan-700 border-4" v-for="i in 100" :key="i">

            </div>
        </div>
        <div class="bg-transparent h-[90vh] w-[70%]">
            <div class="overflow-y-scroll h-[90%] p-5">
                <div class="chat chat-start">
                    <div class="chat-bubble">It's over Anakin, <br />I have the high ground.</div>
                </div>
                <div class="chat chat-end">
                    <div class="chat-bubble">You underestimate my power!</div>
                </div>
            </div>
            <div class="flex h-[10%] gap-4">
                <input v-on:keyup.enter="onSend" v-model="chat" type="text" placeholder="Type here" class="input input-bordered w-[80%] h-full" />
                <button @click="onSend" class="w-[20%] btn h-full">Send</button>
            </div>
        </div>
    </div>
</template>
<style scoped></style>