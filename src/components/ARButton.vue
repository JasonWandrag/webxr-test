<template>
    <button id="arButton" ref="arBtn" disabled>k</button>
</template>
    
<script setup lang='ts'>
    import { onMounted, ref } from 'vue';

    const arBtn = ref<HTMLButtonElement>();
    let xrSession = null as any;
        
        onMounted(()=> {
    if (navigator.xr) {
  navigator.xr.isSessionSupported('immersive-vr')
  .then((isSupported) => {
    alert("Immersive VR Supported: " )
      console.log(isSupported);
    if (isSupported) {
        
      arBtn.value?.addEventListener('click', onButtonClicked);
      if (arBtn.value?.textContent != undefined){
          arBtn.value.textContent = 'Enter XR';
      }
      if (arBtn.value?.disabled != undefined){
          arBtn.value.disabled = false;
      }

    }
  });
}

})

function onButtonClicked() {
  if (!xrSession) {
    navigator.xr?.requestSession('immersive-vr')
    .then((session) => {
      xrSession = session;
      // onSessionStarted() not shown for reasons of brevity and clarity.
    //   onSessionStarted(xrSession);
    });
  } else {
    // Button is a toggle button.
    xrSession.end();
  }
}
</script>
    
<style scoped>
button {
    position: fixed;
    bottom: 30px;
    left: 50%;
    transform: translateX(-50%);
}
</style>