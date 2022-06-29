<template>
  <div
    ref="carousel-outer"
    class="carousel-outer-container"
    @mousedown="sideSlideStart($event)"
    @mouseup="sideSlideEnd"
    @mousemove="sideSlideMove($event)"
    @mouseleave="sideSlideEnd"
  >
    <div ref="carousel-main" class="carousel-main-container">
      <div
        :ref="`card${index}`"
        class="card"
        v-for="(data, index) in cardData"
        :key="index"
      >
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
</template>
<script>
export default {
  name: "CarouselV2",
  data() {
    return {
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
    sideSlideStart(e) {
      const carousel = this.$refs["carousel-outer"];
      this.clickDragActive = true;
      this.scrollStartX = e.pageX - carousel.offsetLeft;
      this.scrollLeft = carousel.scrollLeft;
      console.log({
        pageX: e.pageX,
        carousel: carousel,
        offsetLeft: carousel.offsetLeft,
        scrollLeft: carousel.scrollLeft,
        scrollStartX: this.scrollStartX,
      });
    },
    sideSlideEnd() {
      if (!this.clickDragActive) return;
      const carousel = this.$refs["carousel-outer"];
      console.log("scrollDirectionLeft", this.scrollDirectionLeft);
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
      console.log("SlideEnd", {
        carousel: carousel,
        offsetLeft: carousel.offsetLeft,
        scrollLeft: carousel.scrollLeft,
        scrollStartX: this.scrollStartX,
      });
    },
    sideSlideMove(e) {
      if (!this.clickDragActive) return;
      e.preventDefault();
      const x = e.pageX;
      const walk = x - this.scrollStartX;
      this.$refs["carousel-outer"].scrollLeft = this.scrollLeft - walk;
      // console.log("walk", walk);
      this.scrollDirectionLeft = walk < 0;
      // console.log("scrollDirectionLeft", this.scrollDirectionLeft);
      // if (!this.clickDragActive) return;
      // const carousel = this.$refs.carousel;
      // const x = e.pageX - carousel.offsetLeft;
      // const walk = x - this.scrollStartX;
      // carousel.scrollLeft = this.scrollLeft - walk;
      // this.scrollDirectionLeft = walk < 0;
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
    console.log(this.$refs.console);
    console.log(this.cardSnapPoints);
  },
};
</script>
<style lang="scss" scoped>
.carousel-outer-container {
  width: 100%;
  max-width: 380px;
  height: 300px;
  background-color: #ccc;
  overflow-y: hidden;
  display: flex;
  overflow-x: scroll;
  scroll-behavior: smooth;
  -webkit-overflow-scrolling: touch;
  &::-webkit-scrollbar {
    display: none;
  }
  scrollbar-width: none;
}
.carousel-main-container {
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
  width: 250px;
  display: flex;
  flex-direction: column;
  flex-wrap: nowrap;
  scroll-snap-align: start;
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
</style>
