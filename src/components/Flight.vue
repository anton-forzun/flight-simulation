<template>
    <div>
      <div class="map" >
        <button class="button" :class="{ 'button_stop': isFlying }" @click="toggleFlight">{{ isFlying ? 'Стоп' : 'Почати' }}</button>
        <div class="plane" :style="{ transform: 'translate(' + planePosition.x + 'px, ' + planePosition.y + 'px) rotate(' + planeRotation + 'deg)' }">
          <img src="../assets/plane.svg" />
        </div>
     </div>
    </div>
  </template>
  
  <script>
  import flightData from '../data.js'
  export default {
    data() {
      return {
        flightData: [], 
        currentIndex: 0, 
        planePosition: { x: 0, y: 0 }, 
        planeRotation: 0, 
        intervalId: null, 
        isFlying: false, 
      };
    },
    methods: {
    toggleFlight() {
      if (this.isFlying) {
        this.stopFlight();
      } else {
        this.startFlight();
      }
  },
  startFlight() {
    if (!this.isFlying) {
      let elapsedTime = 0; 

      this.intervalId = setInterval(() => {
        if (this.currentIndex < this.flightData.length && elapsedTime <= 20) {
          const data = this.flightData[this.currentIndex];
          this.updatePlanePosition(data.speed, data.direction);
          this.currentIndex++;
          elapsedTime += 1; 
        } else {
          this.stopFlight();
        }
      }, 1000);
    }
    this.isFlying = true;
  },

stopFlight() {
    clearInterval(this.intervalId); 
    this.isFlying = false;
    this.resetPlanePosition();
  },
resetPlanePosition() {
    // Повернення літака на початкову позицію
    this.planePosition.x = (1240 - 30) / 2; 
    this.planePosition.y = (600 - 30) / 2; 
    this.currentIndex = 0; 
  },
      updatePlanePosition(speed, direction) {
  // Оновлення позиції літака з меншим кроком
  const distance = parseFloat(speed) * 0.05; // масштаб переміщення
  const radians = (parseFloat(direction) - 90) * (Math.PI / 180); // градуси у радіани
  const deltaX = Math.cos(radians) * distance;
  const deltaY = Math.sin(radians) * distance;

  const mapWidth = 1240; 
  const mapHeight = 600; 

  // Обмеження руху літака в межах карти
  const maxX = mapWidth - 30; 
  const maxY = mapHeight - 30; 

  this.planePosition.x = Math.max(0, Math.min(this.planePosition.x + deltaX, maxX));
  this.planePosition.y = Math.max(0, Math.min(this.planePosition.y + deltaY, maxY));

  // Оновлення кута повороту літака
  this.planeRotation = parseFloat(direction);
},
    },
    created() {
      // Отримання даних про польот під час ініціалізації компонента
      this.flightData = flightData;
      // Встановлення початкової позиції літака в центрі карти
    this.planePosition.x = (1240 - 30) / 2; 
    this.planePosition.y = (600 - 30) / 2; 
    }
  };
  </script>
  
  <style>
  .map {
    position: relative;
    width: 1240px; 
    height: 600px; 
    background-image: url(../assets/map.png);
    background-size: cover;
    background-repeat: no-repeat;
  }
  
  .plane {
    position: absolute;
    background: rgba(0, 0, 0, 0.603);
    opacity: 90;
    width: 30px; 
    height: 30px; 
    transition: transform 0.5s ease, top 0.5s ease, left 0.5s ease; 
    
  }
  .plane img {
    width: 30px; 
    height: 30px; 
  }
  button {
    width: 150px;
  }
  .button {
    position: absolute;
    bottom:70px;
    left: 45%;
    background: yellow;
    color: black;
  }
  .button_stop {
    background-color: rgba(0, 0, 0, 0.438);
    color: aliceblue;
  }
  </style>