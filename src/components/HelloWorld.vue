<template>
  <v-container class="fill-height">
    <div v-if="!wrapper.length" class="text-center mt-16">
      <div><img style="max-width: 200px; max-height: 200px;" src="https://hexpeak.co.in/assets/img/Hex-peak.webp"/></div>
      <div><span class="text-h4 text-md-h4 text-lg-h3 text-white">Welcome to Hex-Chat GPT</span></div>
      <span class="text-grey">Feel free to hang around, ask questions, and share your own projects built with Hex-Chat</span>
    </div>
      <div
        v-for="(chat, i) in wrapper"
        :key="i"
        class="my-4"
      >
        <v-alert
          :title="chat.isAi ? 'hexbot' : 'you'"
          :color="!chat.isAi ? 'grey' : 'white'"
          :icon="!chat.isAi ? 'mdi-account-circle-outline' : 'mdi-robot'"
          :variant="chat.isAi?'outlined':'tonal'"
          class="mx-4 mb-12"
          closable 
        >
          {{ chat.value }}
        </v-alert>
      </div>
      <div class="d-flex mx-7" style="position: absolute;bottom:0;width:94%;right:0;z-index:1">
      <v-textarea
          auto-grow
          light
          append-inner-icon="mdi-send-outline"
          v-model="question"
          variant="solo dense"
          rows="1"
          @click:append-inner="fetchAnswer()"
          placeholder="Ask hexChat..."
          row-height="15"
          class=""
        ></v-textarea>
    </div>
  </v-container>
</template>

<script setup>
import { ref } from "vue";
const question = ref("");
const wrapper = ref([]);
const loading = ref(false);

const fetchAnswer = async () => {
  try {
    loading.value = true;
    wrapper.value.push({
      isAi: false,
      value: question.value,
    });
    wrapper.value.push({
      isAi: true,
      value: "Loading....",
    });
    const res = await fetch("http://65.2.128.62", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        question: question.value,
      }),
    });
    // console.log(res);
    const data = await res.json();
    console.log(data);
    const parsedData = data.bot.trim();
    wrapper.value.pop();
    wrapper.value.push({
      isAi: true,
      value: parsedData,
    });
  } finally {
    loading.value = false;
    question.value = "";
  }
};
</script>
