<template>
  <a-button type @click="click()">{{ buttonName }}</a-button>
  <span>{{ status }}</span>
  <a-textarea v-model:value="content" placeholder="Basic usage" :rows="4" />
</template>
<script setup>
import { ref, onMounted } from "vue";
const content = ref("")
const status = ref("Idle");
let recognition = undefined;
const buttonName = ref("start");

const start = () => {
  if (recognition) {
    recognition.start()
  }
}

const end = () => {
  if (recognition) {
    recognition.stop()
  }
}

const click = () => {
  if (buttonName.value == "start") {
    start();
    buttonName.value = "stop";
  } else {
    end();
    buttonName.value = "start";
  }
}

onMounted(() => {
  if ('webkitSpeechRecognition' in window) {
    recognition = new webkitSpeechRecognition();

    recognition.lang = 'zh-CN'; // 设置语言为中文
    recognition.continuous = false; // 禁用连续识别模式
    recognition.interimResults = true;

    recognition.onresult = function (event) {
      // 获取识别结果
      const result = event.results[event.resultIndex];
      const text = result[0].transcript;
      console.log('识别结果：' + text);
      content.value = text;
    };

    recognition.onerror = function (event) {
      console.error('语音识别错误：' + event.error);
    };

    recognition.onstart = function () {
      status.value = "Listening..."
      console.log('Listening started...');
    }

    recognition.onend = function () {
      status.value = "Idle"
      buttonName.value = "start"
      console.log('Listening stopped.');
    }

  } else {
    console.error('Web Speech API is not supported in this browser.');
  }
});
</script>