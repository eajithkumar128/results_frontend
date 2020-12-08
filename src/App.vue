<template>
  <v-app>
    <div>
      <ul>
        <li><a class="active" href="#">MATCH RESULTS</a></li>

        <li style="float: right">
          <a
            target="_blank"
            rel="noopener noreferrer"
            class="active"
            href="https://eajithkumar.tech/"
            >VIEW MY PROFILE</a
          >
        </li>
      </ul>
    </div>
    <v-main class="mt-10">
      <HelloWorld />
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from "./components/HelloWorld";

export default {
  name: "App",

  components: {
    HelloWorld,
  },

  data: () => ({
    //
  }),
};
</script>

<style scoped>
ul {
  list-style-type: none;
  margin: 0;
  padding: 0;
  overflow: hidden;
  background-color: #333;
}

li {
  float: left;
}

li a {
  display: block;
  color: white;
  text-align: center;
  padding: 14px 16px;
  text-decoration: none;
}

li a:hover:not(.active) {
  background-color: #111;
}

.active {
  background-color: #4caf50;
}
</style>
