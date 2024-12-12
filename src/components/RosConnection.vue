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
import ROSLIB from "roslib";
export default {
  data() {
    return {
      connectionStatus: "Disconnected",
      ros: null,
      topic: null,
      messageToSend: "",
      receivedMessage: "",
    };
  },
  methods: {
    connectToRos() {
      // Initialize ROS connection
      this.ros = new ROSLIB.Ros({
        url: "ws://localhost:9090", // Replace with your ROS bridge WebSocket URL
      });
      // Handle connection events
      this.ros.on("connection", () => {
        this.connectionStatus = "Connected";
        console.log("Connected to ROS Bridge");
        this.subscribeToTopic();
      });
      this.ros.on("error", (error) => {
        this.connectionStatus = "Error";
        console.error("Error connecting to ROS Bridge:", error);
      });
      this.ros.on("close", () => {
        this.connectionStatus = "Disconnected";
        console.log("Disconnected from ROS Bridge");
      });
    },
    subscribeToTopic() {
      // Create a topic object
      this.topic = new ROSLIB.Topic({
        ros: this.ros,
        name: "/example_topic", // Replace with your topic name
        messageType: "std_msgs/String", // Replace with your message type
      });
      // Subscribe to the topic
      this.topic.subscribe((message) => {
        this.receivedMessage = message.data;
        console.log("Received message:", message);
      });
    },
    publishMessage() {
      if (!this.topic) {
        console.error("Topic not initialized");
        return;
      }
      // Create a message object
      const message = new ROSLIB.Message({
        data: this.messageToSend,
      });
      // Publish the message
      this.topic.publish(message);
      console.log("Published message:", this.messageToSend);
    },
  },
  mounted() {
    this.connectToRos();
  },
};
</script>















