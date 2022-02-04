<template>
  <section class="user-id">
    <h2>name: {{ user.name }}</h2>
    <p>age: {{ user.age }}</p>
    <p>id: {{ user._id }}</p>
  </section>
</template>

<script>
export default {
  async asyncData({ $axios, params }) {
    const user = await $axios.$get(`http://localhost:3000/users/${params.id}`);
    return { user };
  },
};
</script>

<style></style>
