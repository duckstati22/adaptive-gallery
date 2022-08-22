<template>
  <section @click.prevent="chooseImage" class="gallery">
    <div class="gallery__item" v-for="image in imagesList" :key="image.id">
      <img
        class="gallery__img"
        :src="image.url"
        :data-id="image.id"
        alt="Gallery image"
      />
      <p class="gallery__title">id: {{ image.id }}</p>
    </div>
  </section>
</template>

<script>
export default {
  name: "GalleryComponent",
  emits: ["openModal"],

  data() {
    return {
      imagesList: [],
    };
  },

  created() {
    this.loadImagesList();
  },

  methods: {
    loadImagesList() {
      fetch("https://boiling-refuge-66454.herokuapp.com/images")
        .then((response) => response.json())
        .then((data) => (this.imagesList = data));
    },

    chooseImage(event) {
      if (event.target.tagName === "IMG") {
        const elemId = event.target.getAttribute("data-id");
        this.$emit("openModal", elemId);
      }
    },
  },
};
</script>

<style scoped>
.gallery {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  column-gap: 32px;
  row-gap: 32px;
  padding-top: 46px;
  padding-bottom: 10px;
}
.gallery__item {
  display: flex;
  flex-direction: column;
  row-gap: 8px;
}
.gallery__img {
  width: min(calc(100vw - 20px), 431.33px);
  border-radius: 8px;
}
@media screen and (hover: hover) {
  .gallery__img:hover {
    box-shadow: 0 0 10px black;
    transition: box-shadow 0.2s ease-out;
    cursor: pointer;
  }
}

@media screen and (max-width: 894px) {
  .gallery__title {
    text-align: center;
  }
}
</style>
