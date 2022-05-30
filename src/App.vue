<script setup>
import { RouterLink, RouterView } from "vue-router";
</script>

<template>
  <header class= "main-header">
    <div class="logo-section">
      <h1>TRFO Bird Call Training App</h1>
      <p>Made by <a href="https://schmidtynotes.com">Mike Schmidt</a></p>
    </div>

    <nav class = "main-navigation">
      <RouterLink to="/">Home</RouterLink>
      <RouterLink to="/about">About</RouterLink>
    </nav>
  </header>

  <RouterView />
</template>

<style>
@import "@/assets/base.css";


</style>
