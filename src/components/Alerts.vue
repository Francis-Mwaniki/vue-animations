<template>
    <div>
      <button @click="showNotification">Show Notification</button>
      <transition name="popup">
        <div v-if="showPopup" class="popup">
          <div class="popup-content">
            <h3>New Notification</h3>
            <p>This is a notification from Vue.js!</p>
          </div>
        </div>
      </transition>
    </div>
  </template>
  
  <script>
  import Icon from "../assets/Icon.svg";
  import NotificationSound from "../assets/notification.wav";
  
  export default {
    data() {
      return {
        showPopup: false
      };
    },
    methods: {
      showNotification() {
        if ('Notification' in window) {
          if (Notification.permission === 'granted') {
            this.displayNotification();
          } else if (Notification.permission !== 'denied') {
            Notification.requestPermission().then((permission) => {
              if (permission === 'granted') {
                this.displayNotification();
              }
            });
          }
        } else {
          console.log('Notifications are not supported');
        }
      },
      displayNotification() {
        const options = {
          body: 'This is a notification from Vue.js!',
          icon: Icon,
        };
  
        const notification = new Notification('Vue.js Notification', options);
  
        notification.onclick = () => {
          console.log('Notification clicked');
        };
  
        // Play notification sound
        const sound = new Audio(NotificationSound);
        sound.play();
  
        this.showPopup = true;
        setTimeout(() => {
          this.showPopup = false;
        }, 5000);
      },
    },
  };
  </script>
  
  <style>
  button {
    background-color: #4CAF50;
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
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: #fff;
    border: 1px solid #ccc;
    padding: 20px;
    border-radius: 4px;
    box-shadow: 0px 2px 8px rgba(0, 0, 0, 0.15);
    animation: popupAnimation 0.3s ease-out;
  }
  
  .popup-content {
    text-align: center;
    color: black;
  }
  
  @keyframes popupAnimation {
    0% {
      opacity: 0;
      transform: translate(-50%, -60%);
    }
    100% {
      opacity: 1;
      transform: translate(-50%, -50%);
    }
  }
  
  .popup-enter-active,
  .popup-leave-active {
    transition: opacity 0.3s, transform 0.3s;
  }
  
  .popup-enter,
  .popup-leave-to {
    opacity: 0;
    transform: translate(-50%, -60%);
  }
  </style>
  