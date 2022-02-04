<template>
  <section>
    <h1>This is a test page !</h1>
    <nuxt-link to="/">Go home</nuxt-link>
    <v-file-input
      :rules="rules"
      accept="image/png, image/jpeg, image/gif"
      placeholder="Pick an avatar"
      prepend-icon="mdi-camera"
      label="Avatar"
      show-size
      v-model="file"
    ></v-file-input>

    {{ file }}
  </section>
</template>

<script>
export default {
  async asyncData({ $axios }) {
    let files = await $axios.$get("http://localhost:5050/api/upload");
    files = files.reverse();
    return { files };
  },
  data: () => ({
    file: null,

    rules: [
      (value) =>
        !value ||
        value.size < 2000000 ||
        "Avatar size should be less than 2 MB!",
      (v) => !!v || "This image is required",
    ],
  }),
  watch: {
    files: function (val) {
      console.log(val);
    },
  },
};
</script>

<style></style>
