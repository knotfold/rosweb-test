<template>
  <div>
    <h1>ROS Bridge Connection</h1>
    <p>Status: {{ connectionStatus }}</p>
    <input v-model="messageToSend" placeholder="Type a message to publish" />
    <button @click="publishMessage">Publish</button>
    <p>Received Message: {{ receivedMessage }}</p>
  </div>
</template>

<script>

export default {
  data() {
    return {
      connectionStatus: "Disconnected",
    };
  },
  methods: {
    testWebSocketConnection() {
      const socket = new WebSocket("wss://192.168.1.63:9090");

      socket.onopen = () => {
        this.connectionStatus = "Connected";
        console.log("WebSocket connected");
      };

      socket.onerror = (error) => {
        this.connectionStatus = "Error";
        console.error("WebSocket error:", error);
      };

      socket.onclose = () => {
        this.connectionStatus = "Disconnected";
        console.log("WebSocket disconnected");
      };
    },
  },
  mounted() {
    this.testWebSocketConnection();
  },
};


</script>















