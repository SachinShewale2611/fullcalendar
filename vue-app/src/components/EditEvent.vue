<template>
    <div id="popupOverlay" 
          class="overlay-container"> 
        <div class="popup-box"> 
            <h2 class="font-bold text-lg mb-2"
            >Edit Event</h2> 
            <form class="form-container"> 
                <label class="form-label" 
                        for="name"> 
                  Event name: 
                  </label> 
                <input class="form-input" type="text" 
                        placeholder="Enter event name" 
                        id="name" name="name" required> 
  
                <label class="form-label" for="description">Description:</label> 
                <input class="form-input"
                        type="description" 
                        placeholder="Enter event description"
                        id="description" 
                        name="description" required> 
  
                <button class="btn-submit" 
                        type="submit"> 
                  Submit 
                  </button> 
            </form> 
  
            <button class="btn-close-popup" 
                    @click="$emit('close')"> 
              Close 
              </button> 
        </div> 
    </div> 
  </template>
  <script>
  export default {
    name: 'edit-event',
  }
  
  </script>
  
  <style scoped>
  .overlay-container { 
    position: fixed; 
    top: 0; 
    left: 0; 
    width: 100%; 
    height: 100%; 
    background: rgba(0, 0, 0, 0.6); 
    justify-content: center; 
    align-items: center; 
    /* opacity: 0;  */
    transition: opacity 0.3s ease; 
    z-index: 999;
    display: flex;
  } 
    
  .popup-box { 
      background: #fff; 
      padding: 24px; 
      border-radius: 12px; 
      box-shadow: 0 0 20px rgba(0, 0, 0, 0.4); 
      width: 320px; 
      text-align: center; 
      opacity: 0; 
      transform: scale(0.8); 
      animation: fadeInUp 0.5s ease-out forwards; 
  } 
  
  .form-container { 
      display: flex; 
      flex-direction: column; 
  } 
  
  .form-label { 
      margin-bottom: 10px; 
      font-size: 16px; 
      color: #444; 
      text-align: left; 
  } 
  
  .form-input { 
      padding: 10px; 
      margin-bottom: 20px; 
      border: 1px solid #ccc; 
      border-radius: 8px; 
      font-size: 16px; 
      width: 100%; 
      box-sizing: border-box; 
  } 
  
  .btn-submit, 
  .btn-close-popup { 
      padding: 12px 24px; 
      border: none; 
      border-radius: 8px; 
      cursor: pointer; 
      transition: background-color 0.3s ease, color 0.3s ease; 
  } 
  
  .btn-submit { 
      background-color: green; 
      color: #fff; 
  } 
  
  .btn-close-popup { 
      margin-top: 12px; 
      background-color: #e74c3c; 
      color: #fff; 
  } 
  
  .btn-submit:hover, 
  .btn-close-popup:hover { 
      background-color: #4caf50; 
  } 
    
          /* Keyframes for fadeInUp animation */ 
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
    
          /* Animation for popup */ 
          .overlay-container.show { 
              display: flex; 
              opacity: 1; 
          } 
  </style>