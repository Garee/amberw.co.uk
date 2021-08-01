<template>
  <p class="happy-birthday" v-if="isBirthday() && !playing" v-on:click="onOpenGift()">
    <span class="gift">🎁</span> Open your birthday gift!
  </p>
</template>

<script>
export default {
  name: 'HappyBirthday',
  data() {
    return {
      colors: ['yellow', 'green', 'blue', 'red', 'purple'],
      density: 10,
      playing: false,
    }
  },
  methods: {
    onOpenGift() {
      this.playing = true
      document.body.style = 'overflow: hidden'
      this.spawnBalloons()
      const song = new Audio('/audio/happy-birthday.mp3')
      song.addEventListener('ended', () => {
        this.playing = false
      })
      song.play()
    },
    isBirthday() {
      const now = new Date()
      return now.getDate() === 12 && now.getMonth() + 1 === 8
    },
    spawnBalloons() {
      const string = document.createElement('div')
      string.classList.add('string')

      for (let i = 0; i < this.density; i++) {
        const element = document.createElement('div')
        element.classList.add('balloon')
        element.classList.add(this.getRandomColor())

        element.append(string.cloneNode())
        document.body.append(element)

        setTimeout(() => {
          this.spawnBalloon(element)
        }, i * 2000 + this.getRandomInt(500, 1000))
      }
    },
    spawnBalloon(balloon) {
      const delay = this.getRandomInt(100, 1000)
      const x = this.getRandomInt(-99, -30)
      const y = this.getRandomInt(-99, -30)

      const sequence = [
        {
          offset: 0,
          transform: `rotateZ(45deg) translate(0, 0)`,
        },
      ]

      if (this.getRandomInt(0, 2) === 0) {
        balloon.style.left = `${-1 * x}vw`

        sequence.push({
          offset: x / -200,
          transform: `rotateZ(45deg) translate(${x}vw, 0)`,
        })
        sequence.push({
          offset: (x + y) / -200,
          transform: `rotateZ(45deg) translate(${x}vw, ${y}vh)`,
        })
        sequence.push({
          offset: (-100 + y) / -200,
          transform: `rotateZ(45deg) translate(-100vw, ${y}vh)`,
        })
      } else {
        sequence.push({
          offset: y / -200,
          transform: `rotateZ(45deg) translate(0, ${y}vh)`,
        })
        sequence.push({
          offset: (x + y) / -200,
          transform: `rotateZ(45deg) translate(${x}vw, ${y}vh)`,
        })
        sequence.push({
          offset: (-100 + x) / -200,
          transform: `rotateZ(45deg) translate(${x}vw, -100vh)`,
        })
      }

      sequence.push({
        offset: 1,
        transform: `rotateZ(45deg) translate(-100vw, -100vh)`,
      })

      const balloonAnimation = balloon.animate(sequence, {
        duration: 20000,
        delay: delay,
      })

      balloonAnimation.onfinish = () => {
        this.spawnBalloon(balloon)
      }
    },
    getRandomColor() {
      return this.colors[this.getRandomInt(0, this.colors.length)]
    },
    getRandomInt(min, max) {
      return Math.floor(Math.random() * (max - min)) + min
    },
  },
}
</script>

<style lang="postcss">
.balloon {
  --balloonDimension: 10vmax; /* 15% of min(viewport width, height) */
  width: var(--balloonDimension);
  height: var(--balloonDimension);
  border-radius: 100% 100% 15% 100%;
  margin: 0 0 0 25px;
  transform: rotateZ(45deg);
  position: fixed;
  bottom: calc(-1 * var(--balloonDimension));
  left: 0;
  background-color: aqua;
  z-index: 9999;
}

.balloon::before {
  content: '';
  width: 10%;
  height: 25%;
  background: radial-gradient(circle, rgba(255, 255, 255, 0.7) 0%, rgba(255, 255, 255, 0.1) 100%);
  position: absolute;
  left: 15%;
  top: 45%;
  border-radius: 100%;
}

.balloon::after {
  content: '';
  width: 13%;
  height: 5%;
  background-color: inherit;
  position: absolute;
  left: 90%;
  top: 94%;
  border-radius: 22%;
  transform: rotateZ(-45deg);
}

.balloon .string {
  position: absolute;
  /*background-color: #990;background-color */
  background-color: white;
  width: 2px;
  height: calc(var(--balloonDimension) * 0.6);
  transform-origin: top center;
  transform: rotateZ(-45deg);
  top: calc(var(--balloonDimension) - 6px);
  left: calc(var(--balloonDimension) - 8px);
}

.yellow {
  background-color: rgba(150, 150, 0, 0.45);
}

.green {
  background-color: rgba(0, 150, 0, 0.45);
}

.blue {
  background-color: rgba(0, 0, 150, 0.45);
}

.red {
  background-color: rgba(150, 0, 0, 0.45);
}

.purple {
  background-color: rgba(148, 0, 211, 0.45);
}

.happy-birthday {
  width: 100%;
  text-align: center;
  position: relative;
  font-size: 1.5rem;
  cursor: pointer;

  &:hover {
    color: yellow;
  }
}
</style>
