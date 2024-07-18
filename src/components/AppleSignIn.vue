<template>
  <div class="flex items-center justify-center min-h-screen bg-gray-100">
    <div id="appleid-signin" class="apple-signin-button" data-type="continue" data-color="black" data-border="true" ></div>
  </div>
</template>

<script setup>
import { onMounted } from 'vue';

onMounted(() => {
  const loadAppleScript = () => {
    const script = document.createElement('script');
    script.src = 'https://appleid.cdn-apple.com/appleauth/static/jsapi/appleid/1/en_US/appleid.auth.js';
    script.onload = initializeAppleSignIn;
    document.head.appendChild(script);
  };

  const initializeAppleSignIn = () => {
    window.AppleID.auth.init({
      clientId: 'mock-client-id-for-testing', // Mock client ID for testing
      scope: 'name email',
      redirectURI: 'https://yourdomain.com/callback', // Replace with your actual redirect URI
      usePopup: true,
    });
  };

  loadAppleScript();
});
</script>

<style scoped>
.apple-signin-button {
  width: 200px;
  height: 44px;
}
</style>
