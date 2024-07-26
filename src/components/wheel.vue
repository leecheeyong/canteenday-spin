<script lang="ts" setup>
import { ref, computed, onMounted } from 'vue'
import FortuneWheel from 'vue-fortune-wheel'
import 'vue-fortune-wheel/style.css'

const data = await fetch('https://canteenday-2024.vercel.app/wheel').then(r=>r.json())
const prizeId = ref(0)
const prizesCanvas = data.map((i) => {
    return {
        id: i["id"],
        name: i["name"],
        value: i["value"],
        bgColor: i["bgColor"],
        color: "#ffffff",
        probability: i["probability"]
    }
})

const wheelEl = ref()
const canvasVerify = ref(false) // Whether the turntable in canvas mode is enabled for verification
const verifyDuration = 2
const canvasOptions = {
  btnWidth: 140,
  borderColor: '#584b43',
  borderWidth: 6,
  lineHeight: 30
}

const prizeRes = computed(() => {
  return prizesCanvas.find(item => item.id === prizeId.value) || prizesCanvas[0]
})


onMounted(() => {
  wheelEl.value.startRotate() // Can start rotation
})

// Simulate the request back-end interface
function testRequest (verified, duration) { // verified: whether to pass the verification, duration: delay time
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(verified)
    }, duration)
  })
}

function onCanvasRotateStart (rotate) {
  if (canvasVerify.value) {
    const verified = true // true: the test passed the verification, false: the test failed the verification
    testRequest(verified, verifyDuration * 1000).then((verifiedRes) => {
      if (verifiedRes) {
        console.log('Verification passed, start to rotate')
        rotate() // Call the callback, start spinning
        canvasVerify.value = false // Turn off verification mode
      } else {
        alert('Failed verification')
      }
    })
    return
  }
  console.log('onCanvasRotateStart')
}

function onImageRotateStart () {
  console.log('onImageRotateStart')
}

function onRotateEnd (prize) {
  alert(prize.value)
}

function onChangePrize (id) {
  prizeId.value = id
}
</script>
<template>
    <FortuneWheel
      style="width: 500px; max-width: 100%;"
      :verify="canvasVerify"
      :canvas="canvasOptions"
      :prizes="prizesCanvas"
      @rotateStart="onCanvasRotateStart"
      @rotateEnd="onRotateEnd"
    />
</template>