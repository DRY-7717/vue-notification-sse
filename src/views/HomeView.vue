<script setup>
import { onMounted, ref } from "vue";
import { fetchEventSource } from "@microsoft/fetch-event-source";

const notifications = ref([]);

// const startSSE = () => {
//   const eventSource = new EventSource("http://localhost:5062/api/notification/stream/userguide@bi.go.id");

//   eventSource.onmessage = (event) => {
//     notifications.value = JSON.parse(event.data);
//   };

//   eventSource.onerror = () => {
//     eventSource.close();
//   };
// };

const startSSE = () => {
  fetchEventSource("https://localhost:8004/api/notification/stream/userguide@bi.go.id", {
    // headers: {
    //   "Authorization": `Bearer ${token}`
    // },
    onmessage(event) {
      notifications.value = JSON.parse(event.data);
    },
    onerror(err) {
      console.error("SSE Error:", err);
    },
    onclose() {
      console.log("SSE connection closed, attempting to reconnect...");
      setTimeout(startSSE, 5000); // Reconnect setelah 5 detik
    }
  });

};

onMounted(() => {
  startSSE();
});


</script>

<template>
  <div>
    <h2>Notifikasi:</h2>
    <ul>
      <li v-for="(notification, index) in notifications" :key="index">
        {{ notification.title }}
      </li>
    </ul>
  </div>
</template>
