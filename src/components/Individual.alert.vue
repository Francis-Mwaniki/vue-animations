<template>
    <div>
      <button @click="showNotification">Show Specific</button>
      <transition name="popup">
        <div v-if="showPopup" class="popup">
          <div class="popup-content">
            <h3>New Notification</h3>
            <div v-for="(message, index) in messages" :key="index" class="msg-index" :style="{ backgroundColor: getColor(index) }">
              <p>{{ message }}</p>
            </div>
          </div>
        </div>
      </transition>
    </div>
  </template>
  
  <script>
  import Icon from "../assets/Icon.svg";
  import NotificationSound from "../assets/joke.wav";
  
  export default {
    data() {
      return {
        showPopup: false,
        messages: [],
      };
    },
    methods: {
      showNotification() {
        if ("Notification" in window) {
          if (Notification.permission === "granted") {
            this.displayNotification();
          } else if (Notification.permission !== "denied") {
            Notification.requestPermission().then((permission) => {
              if (permission === "granted") {
                this.displayNotification();
              }
            });
          }
        } else {
          console.log("Notifications are not supported");
        }
      },
      displayNotification() {
        const options = {
          body: "This is a notification from Vue.js!",
          icon: Icon,
        };
  
        const notification = new Notification("Vue.js Notification", options);
  
        notification.onclick = () => {
          console.log("Notification clicked");
        };
  
        // Play notification sound
        const sound = new Audio(NotificationSound);
        sound.play();
  
        this.showPopup = true;
        this.messages = [];
  
        setTimeout(() => {
          this.messages.push("Message 1");
          setTimeout(() => {
            this.messages.push("Message 2");
            setTimeout(() => {
              this.messages.push("Message 3");
              setTimeout(() => {
                this.showPopup = false;
              }, 5000);
            }, 4000);
          }, 3000);
        }, 1000);
      },
      getColor(index) {
        const colors = ["#FFC107", "#3F51B5", "#E91E63"];
        return colors[index % colors.length];
      },
    },
  };
  </script>
  
  <style>
  button {
    background-color: #4caf50;
    border: none;
    color: white;
    padding: 15px 32px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
  }
  
  .popup {
    position: fixed;
    bottom: 2px;
    right: 1px;
    background-color:transparent;
    padding: 2px;
    border: none;
    width: 300px;
    height: 200px;
    border-radius: 4px;
    animation: popupAnimation 0.3s ease-out;
    transform: translate(50%, 50%);
  }
  
  .popup-content {
    text-align: center;
  }
  
  @keyframes popupAnimation {
    0% {
      opacity: 0;
      transform: translate(50%, 60%);
    }
    100% {
      opacity: 1;
      transform: translate(50%, 50%);
    }
  }
  
  .popup-enter-active,
  .popup-leave-active {
    transition: opacity 0.3s, transform 0.3s;
  }
  
  .popup-enter,
  .popup-leave-to {
    opacity: 0;
    transform: translate(50%, 60%);
  }
  .msg-index {
    padding: 4px;
    margin: 1px;
    border-radius: 4px;
  }
  </style>
  