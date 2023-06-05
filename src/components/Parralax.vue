<template>
    <div id="universe"></div>
  </template>
  
  <script>
  export default {
    mounted() {
      const layerCount = 4;
      const starCount = 500;
      const maxTime = 31;
      const universe = document.getElementById('universe');
      const { innerWidth: width, innerHeight: height } = window;
  
      for (let i = 0; i < starCount; ++i) {
        const ypos = Math.round(Math.random() * height);
        const star = document.createElement('div');
        const speed = 1000 * (Math.random() * maxTime + 1);
        star.setAttribute('class', 'star' + (3 - Math.floor((speed / 1000) / 8)));
        star.style.backgroundColor = 'white'; // You can customize the star color here
        universe.appendChild(star);
        star.animate(
          [
            { transform: `translate3d(${width}px, ${ypos}px, 0)` },
            { transform: `translate3d(-${Math.random() * 256}px, ${ypos}px, 0)` },
          ],
          { delay: Math.random() * -speed, duration: speed, iterations: 1000 }
        );
      }
    },
  };
  </script>
  
  <style>
  body {
    background-color: black;
    overflow: hidden;
    width: 100%;
  }
  
  .star0 {
    height: 1px;
    width: 1px;
    opacity: 1;
    position: absolute;
  }
  
  .star1 {
    height: 2px;
    width: 2px;
    border-radius: 50%;
    opacity: 1;
    position: absolute;
  }
  
  .star2 {
    height: 3px;
    width: 3px;
    border-radius: 50%;
    opacity: 1;
    position: absolute;
  }
  
  .star3 {
    height: 4px;
    width: 4px;
    border-radius: 50%;
    opacity: 1;
    position: absolute;
  }
  </style>
  