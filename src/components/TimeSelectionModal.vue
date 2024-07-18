<template>
    <div id="popupOverlay" class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-60 z-50">
        <div class="bg-white p-6 rounded-lg shadow-lg w-80 animate-fadeInUp">
            <h2 class="font-bold text-lg mb-4">Add Show Time</h2>
            <!-- select hr minutes -->
            <div class="mb-2">
                <label for="time" class="block mb-1">Time</label>
                <input type="time" id="time" v-model="time" name="time" class="w-full p-2 border rounded">
            </div>
            <button @click="addShowTime" class="w-full mt-4 py-2 px-4 bg-green-500 text-white rounded-lg hover:bg-green-600 transition-colors duration-300">Add</button>
            <button @click="closeEdit" class="w-full mt-4 py-2 px-4 bg-red-500 text-white rounded-lg hover:bg-red-600 transition-colors duration-300">Close</button>
        </div>
    </div>
</template>
<script setup>
import { ref, defineEmits } from 'vue';

const emit = defineEmits(['add', 'close']);

const time = ref('');

const addShowTime = () => {
  emit('add', time.value);
};

const closeEdit = () => {
  emit('close');
};

</script>