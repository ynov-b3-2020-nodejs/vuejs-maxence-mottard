<template>
  <layout-data :data="images.length > 0" title="Manage Images">
    <reload-bar :fetch-data-function="fetchImages" />

    <form class="form-inline" @submit="handleAddImage">
      <div class="form-group mb-2">
        <label for="urlNewImage" class="sr-only">New Image URL</label>
        <input
          type="text"
          class="form-control"
          id="urlNewImage"
          v-model="newUrl"
        />
      </div>
      <button type="submit" class="btn btn-primary mb-2">Add</button>
    </form>

    <div class="row text-center text-lg-left">
      <div
        v-for="image in images"
        :key="image._id"
        class="col-lg-3 col-md-4 col-6"
      >
        <img class="img-fluid img-thumbnail" :src="image.url" alt="empty" />
        <div class="input-group mb-3">
          <input type="text" class="form-control" disabled :value="image.url" />
        </div>
      </div>
    </div>
  </layout-data>
</template>

<script>
import LayoutData from "../layouts/LayoutData";
import { createImage, getImages } from "../services/ImagesService";
import ReloadBar from "../components/ReloadBar";
import { mapActions } from "vuex";

export default {
  name: "Images",
  components: {
    LayoutData,
    ReloadBar
  },
  data() {
    return {
      images: [],
      newUrl: ""
    };
  },
  beforeMount() {
    this.fetchImages();
  },
  methods: {
    ...mapActions(["addNotification"]),
    async fetchImages() {
      const result = await getImages();

      if (!result) {
        return;
      }

      this.images = result;
    },
    async handleAddImage(e) {
      e.preventDefault();
      try {
        await createImage({ url: this.newUrl });
        await this.fetchImages();
        this.addNotification({
          title: "Image",
          content: "A new image is now stored."
        });
      } catch (e) {
        this.addNotification({
          title: "Image",
          content: "Une erreur est survenue lors de l'ajout de l'image"
        });
      }
      this.newUrl = "";
    }
  }
};
</script>

<style scoped></style>
