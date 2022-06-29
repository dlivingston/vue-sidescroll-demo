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
          <button class="btn-select" @click="send(data.coverage)">Select</button>
        </div>
      </div>
    </div>
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
};
</script>
<style lang="scss" scoped>
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
    &:hover {
      opacity: 0.8;
    }
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
