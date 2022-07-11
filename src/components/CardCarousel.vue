<template>
  <div class="component-wrapper">
    <div
      class="carousel-container"
      ref="carousel"
      @mousedown="sideSlideStart($event)"
      @mouseup="sideSlideEnd"
      @mousemove="sideSlideMove($event)"
      @mouseleave="sideSlideEnd"
    >
      <div class="carousel-inner-wrapper">
        <div class="card" v-for="(data, index) in cardData" :key="index">
          <h2>{{ data.coverage }}</h2>
          <div class="coverage-info">
            <div><strong>Vehicle Premium:</strong><br />{{ data.premium }}</div>
            <div><strong>Deductible:</strong><br />{{ data.deductible }}</div>
            <div><strong>Limit:</strong><br />{{ data.limit }}</div>
          </div>
          <button class="btn-select" @click="send(data.coverage)">
            Select
          </button>
        </div>
      </div>
    </div>
    <button
      aria-label="Swipe previous slide"
      class="carousel-button carousel-button-prev"
      :disabled="prevDisabled"
      :class="{ hidden: prevDisabled }"
      @click="slidePrev"
    >
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 240.823 240.823">
        <path
          d="M57.633 129.007L165.93 237.268c4.752 4.74 12.451 4.74 17.215 0 4.752-4.74 4.752-12.439 0-17.179l-99.707-99.671 99.695-99.671c4.752-4.74 4.752-12.439 0-17.191-4.752-4.74-12.463-4.74-17.215 0L57.621 111.816c-4.679 4.691-4.679 12.511.012 17.191z"
        ></path>
      </svg>
    </button>
    <button
      aria-label="Swipe next slide"
      class="carousel-button carousel-button-next"
      :disabled="nextDisabled"
      :class="{ hidden: nextDisabled }"
      @click="slideNext"
    >
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 240.823 240.823">
        <path
          d="M183.189 111.816L74.892 3.555c-4.752-4.74-12.451-4.74-17.215 0-4.752 4.74-4.752 12.439 0 17.179l99.707 99.671-99.695 99.671c-4.752 4.74-4.752 12.439 0 17.191 4.752 4.74 12.463 4.74 17.215 0l108.297-108.261c4.68-4.691 4.68-12.511-.012-17.19z"
        ></path>
      </svg>
    </button>
    <div class="button-wrapper">
      <button @click="toggleListView()">List View</button>
      <button @click="button3Send()">Done</button>
    </div>
    <div v-if="listViewShow" class="list-view">
      <div class="list-view-card">
        <div class="card-header">
          <h2>Vehicle Coverages</h2>
          <button @click="toggleListView()">X</button>
        </div>
        <table>
          <thead>
            <tr>
              <td>Coverage</td>
              <td>Premium</td>
              <td>Limit</td>
              <td>Deductible</td>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(data, index) in cardData" :key="index">
              <td>{{ data.coverage }}</td>
              <td>{{ data.premium }}</td>
              <td>{{ data.limit }}</td>
              <td>{{ data.deductible }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "CardCarousel",
  data() {
    return {
      listViewShow: false,
      clickDragActive: false,
      scrollStartX: 0,
      scrollLeft: 0,
      scrollDirectionLeft: true,
      currentSnapPoint: 0,
      nextDisabled: false,
      prevDisabled: true,
      cardData: [
        {
          coverage: "Bodily Injury Liability",
          premium: 200,
          limit: 250000,
          deductible: 200,
        },
        {
          coverage: "Supplementary Uninsured Motorist",
          premium: 200,
          limit: 250000,
          deductible: 200,
        },
        {
          coverage: "Medical Payments",
          premium: 200,
          limit: 250000,
          deductible: 200,
        },
        {
          coverage: "Comprehensive",
          premium: 200,
          limit: 250000,
          deductible: 200,
        },
        {
          coverage: "Emergency Road Service",
          premium: 200,
          limit: 250000,
          deductible: 200,
        },
        {
          coverage: "Bodily Injury Liability",
          premium: 200,
          limit: 250000,
          deductible: 200,
        },
      ],
    };
  },
  methods: {
    send(msg) {
      this.sendMessage({
        message: msg,
        myData: msg,
      });
    },
    button3Send() {
      console.log("button 3 test");
    },
    toggleListView() {
      this.listViewShow = !this.listViewShow;
    },
    sideSlideStart(e) {
      const carousel = this.$refs.carousel;
      this.clickDragActive = true;
      this.scrollStartX = e.pageX - carousel.offsetLeft;
      this.scrollLeft = carousel.scrollLeft;
    },
    sideSlideEnd() {
      if (!this.clickDragActive) return;
      const carousel = this.$refs.carousel;
      if (this.scrollDirectionLeft) {
        carousel.scrollLeft = this.cardSnapPoints.find(
          (e) => e > carousel.scrollLeft
        );
      }
      if (!this.scrollDirectionLeft) {
        carousel.scrollLeft = this.cardSnapPoints
          .reverse()
          .find((e) => e < carousel.scrollLeft);
      }
      this.clickDragActive = false;
    },
    sideSlideMove(e) {
      if (!this.clickDragActive) return;
      e.preventDefault();
      const x = e.pageX;
      const walk = x - this.scrollStartX;
      this.$refs.carousel.scrollLeft = this.scrollLeft - walk;
      this.scrollDirectionLeft = walk < 0;
    },
    slideNext() {
      const carousel = this.$refs.carousel;
      let next = this.currentSnapPoint + 1;
      if (next < this.cardSnapPoints.length) {
        carousel.scrollLeft = this.cardSnapPoints[next];
        this.currentSnapPoint = next;
        this.prevDisabled = false;
        this.nextDisabled =
          this.currentSnapPoint === this.cardSnapPoints.length - 1;
      }
    },
    slidePrev() {
      const carousel = this.$refs.carousel;
      let prev = this.currentSnapPoint - 1;
      if (prev >= 0) {
        this.nextDisabled = false;
        carousel.scrollLeft = this.cardSnapPoints[prev];
        this.currentSnapPoint = prev;
        this.prevDisabled = this.currentSnapPoint === 0;
      }
    },
  },
  computed: {
    cardSnapPoints() {
      const startPoints = [];
      const cardWidth = 266;
      let interval = 0;
      this.cardData.forEach(() => {
        startPoints.push(interval);
        interval = interval + cardWidth;
      });
      return startPoints;
    },
  },
  mounted() {
    this.nextDisabled = this.cardSnapPoints.length === 1;
  },
};
</script>
<style lang="scss" scoped>
.component-wrapper {
  position: relative;
}
.carousel-container {
  width: 100%;
  height: 300px;
  overflow-y: hidden;
  overflow-x: scroll;
  display: flex;
  flex-wrap: nowrap;
  scrollbar-width: none;
  scroll-behavior: smooth;
  -webkit-overflow-scrolling: touch;
  &::-webkit-scrollbar {
    display: none;
  }
}
.carousel-inner-wrapper {
  display: flex;
  flex-wrap: nowrap;
}
.card {
  background-color: var(--messageBackground);
  color: var(--textColor);
  padding: 16px;
  flex: 0 0 250px;
  margin-right: 16px;
  border-radius: 8px;
  display: flex;
  flex-direction: column;
  flex-wrap: nowrap;
  width: 250px;
  cursor: grab;
  button {
    margin-top: auto;
    background: var(--rwcTheme);
    color: var(--c-white);
    transition: opacity 0.4s;
    width: 100%;
    height: 36px;
    padding: 9px 10px;
    outline: none;
    border: 1px solid var(--rwcTheme);
    border-radius: 4px;
    font-size: var(--fontSize);
    line-height: 1.2;
    transition: opacity 0.4s;
    cursor: pointer;
    &:hover {
      opacity: 0.8;
    }
  }
}
.carousel-button {
  position: absolute;
  top: 50%;
  left: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 30px;
  height: 30px;
  cursor: pointer;
  border: none;
  border-radius: 50%;
  opacity: 0.6;
  transition: opacity 0.4s, visibility 0.4s;
  transform: translateY(-50%);
  &.carousel-button-next {
    right: 8px;
    left: auto;
  }
  svg {
    height: 12px;
  }
  &.hidden {
    pointer-events: none;
    visibility: hidden;
    opacity: 0;
  }
  &:hover {
    opacity: 1;
  }
}
.button-wrapper {
  margin-top: 32px;
  button {
    border: 1px solid var(--rwcTheme);
    border-radius: 4px;
    font-size: var(--fontSize);
    line-height: 1.2;
    color: var(--rwcTheme);
    background: var(--messageBackground);
    height: 36px;
    padding: 9px 10px;
    transition: opacity 0.4s;
    &:hover {
      opacity: 0.8;
      background: var(--rwcTheme);
      color: var(--c-white);
    }
  }
}
.list-view {
  position: absolute;
  background: rgba(0, 0, 0, 0.5);
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  z-index: 1000;
  .list-view-card {
    background-color: var(--messageBackground);
    color: var(--textColor);
    padding: 16px 0;
    border-radius: 16px;
    margin: 16px;
    .card-header {
      display: flex;
      flex-direction: row;
      flex-wrap: nowrap;
      justify-content: space-between;
      padding: 0 16px;
    }
  }
}
</style>
