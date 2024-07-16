<template>
    <div id="popupOverlay" class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-60 z-50">
      <div class="bg-white p-6 rounded-lg shadow-lg w-80 animate-fadeInUp">
        <h2 class="font-bold text-lg mb-4">Edit Event</h2>
        <form class="space-y-4">
          <div>
            <label class="block text-left mb-1 text-gray-700" for="name">Event name:</label>
            <input class="w-full p-2 border border-gray-300 rounded-lg" type="text" placeholder="Enter event name" id="name" name="name" required>
          </div>
          <div>
            <label class="block text-left mb-1 text-gray-700" for="description">Description:</label>
            <input class="w-full p-2 border border-gray-300 rounded-lg" type="text" placeholder="Enter event description" id="description" name="description" required>
          </div>
          <button class="w-full py-2 px-4 bg-green-500 text-white rounded-lg hover:bg-green-600 transition-colors duration-300" type="submit">Submit</button>
        </form>
        <button class="w-full mt-4 py-2 px-4 bg-red-500 text-white rounded-lg hover:bg-red-600 transition-colors duration-300" @click="$emit('close')">Close</button>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'edit-event',
  }
  </script>
  
  <style scoped>
  @keyframes fadeInUp {
    from {
      opacity: 0;
      transform: translateY(20px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }
  .animate-fadeInUp {
    animation: fadeInUp 0.5s ease-out forwards;
  }
  </style>
  