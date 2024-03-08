<template>
  <div id="app">
    <main>
      <RegistrationForm />
    </main>
  </div>
</template>

<script>
import RegistrationForm from './components/RegistrationForm.vue';

export default {
  name: 'App',
  components: {
    RegistrationForm,
  },
};
</script>

<style lang="scss">
body {
  position: relative;
  margin: 0;
  font-family: 'Montserrat', sans-serif;
  margin-left: auto;
  margin-right: auto;
  object-fit: cover;
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center;
  font-size: 14px;
  font-style: normal;
}

img {
  display: block;
  max-width: 100%;
}

h1,
h2,
h3,
h4,
h5,
h6,
p {
  margin: 0;
}

ul,
li {
  margin: 0;
  padding: 0;
  list-style: none;
}

a {
  color: #222;
  text-decoration: none;
  transition: color 0.6s ease;
}

button {
  padding: 0;
  border: none;
  background: none;
  cursor: pointer;
}

.container {
  max-width: 1230px;
  margin: 0 auto;
  padding: 0 15px;
}
</style>

