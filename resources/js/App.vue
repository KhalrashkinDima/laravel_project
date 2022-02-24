<template>
  <Header />
  <main class="d-flex main_window">
    <LeftAdv />
    <div class="col-12 col-md-8">
      <router-view />
    </div>
    <RightAdv />
  </main>
  <Footer />
</template>

<script>
import Header from "./components/Header.vue";
import LeftAdv from "./components/LeftAdv.vue";
import RightAdv from "./components/RightAdv.vue";
import Footer from "./components/Footer.vue";

export default {
  name: "App",
  components: {
    Header,
    LeftAdv,
    RightAdv,
    Footer,
  },
};
</script>

<style>
.main_window {
  list-style: none;
  text-decoration: none;
  margin: 0 auto;
  max-width: 1420px;
  padding-bottom: 50px;
  font-family: var(--bs-body-font-family);
  font-weight: var(--bs-body-font-weight);
  line-height: var(--bs-body-line-height);
}

a {
  text-decoration: none;
  color: black;
}

html {
  height: 100%;
}

body {
  position: relative;
  min-height: 100%;
}
</style>
