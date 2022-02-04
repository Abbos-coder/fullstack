<template>
  <section>
    <h1 class="text-center">{{ title }}</h1>
    <nuxt-link to="/test">go to test page</nuxt-link>

    <v-row class="my-10">
      <v-col cols="12" lg="4" md="4" sm="6" xs="12">
        <v-text-field
          label="Name"
          ref="name"
          placeholder="User name"
          v-model="client.name"
          :rules="[(v) => !!v || 'This name is required']"
        ></v-text-field>
      </v-col>
      <v-col cols="12" lg="4" md="4" sm="6" xs="12">
        <v-text-field
          label="Age"
          placeholder="User age"
          v-model="client.age"
          :rules="[(v) => !!v || 'This age is required']"
        ></v-text-field
      ></v-col>
      <v-col cols="12" lg="4" md="4" sm="6" xs="12">
        <v-file-input
          :rules="rules"
          accept="image/png, image/jpeg, image/gif"
          placeholder="Pick an avatar"
          prepend-icon="mdi-camera"
          label="Avatar"
          show-size
          v-model="client.avatar"
        ></v-file-input>
      </v-col>

      <!--Submit button this -->
      <v-col cols="12" class="d-flex justify-center">
        <v-btn
          color="primary"
          large
          rounded
          class="text-center"
          @click="postData(), submitFiles()"
          >submit</v-btn
        >
      </v-col>
    </v-row>
    <!-- cards this -->
    <v-row class="mt-12">
      <v-col
        cols="12"
        lg="4"
        md="4"
        sm="6"
        xs="12"
        v-for="user in users"
        :key="user.id"
      >
        <v-card elevation="2" class="pa-5">
          <v-card-title>{{ user.name }}</v-card-title>
          <v-card-text>age: {{ user.age }}</v-card-text>
          <v-card-text>id: {{ user._id }}</v-card-text>
          <v-card-text>
            <v-row>
              <v-btn
                color="success"
                small
                @click.prevent="$router.push(`/${user._id}`)"
                >view more</v-btn
              >
              <v-spacer></v-spacer>
              <v-btn color="error" small @click.prevent="deleteUser(user._id)"
                >delete</v-btn
              >
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </section>
</template>

<script>
export default {
  async asyncData({ $axios }) {
    let users = await $axios.$get("http://localhost:3000/users");
    users = users.reverse();
    return { users };
  },
  data: () => ({
    title: "My fulrest API !",
    client: {
      name: "",
      age: "",
      avatar: null,
    },
    rules: [
      (value) =>
        !value ||
        value.size < 2000000 ||
        "Avatar size should be less than 2 MB!",
      (v) => !!v || "This image is required",
    ],
  }),
  methods: {
    async postData() {
      try {
        await this.$axios.post("/add_user", this.client);

        this.users = (
          await this.$axios.$get("http://localhost:3000/users")
        ).reverse();

        this.client.name = "";
        this.client.age = "";
        this.client.avatar = null;
      } catch (error) {
        console.log(error);
      }
    },

    async deleteUser(id) {
      await this.$axios.delete(`/users/${id}`);

      this.users = (
        await this.$axios.$get("http://localhost:3000/users")
      ).reverse();
    },

    async submitFiles() {
      if (this.client.avatar) {
        let formData = new FormData();
        formData.append("files", this.client.avatar);

        await this.$axios
          .post("/upload-files", formData)
          .then((response) => {
            console.log("Success!");
            console.log({ response });
          })
          .catch((error) => {
            console.log({ error });
          });
      } else {
        console.log("there are no files.");
      }
    },

    // variable - snake_case
    // function - cameleCase
  },
};
</script>

<style scoped></style>
