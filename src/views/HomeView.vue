<template>
  <hello-world />
</template>

<script>
  import HelloWorld from '../components/HelloWorld'

  export default {
    name: 'HoMe',

    components: {
      HelloWorld,
    },
  }
</script>
<style>
html,
body,
.v-application,
.v-application .display-1,
.v-application .headline,
.v-application .title,
.v-application .subtitle-1,
.v-application .subtitle-2,
.v-application .body-1,
.v-application .body-2,
.v-application .caption,
.v-application .overline {
  font-family: 'Inter var', sans-serif !important;
  /* -webkit-font-smoothing: antialiased !important;
  -moz-osx-font-smoothing: grayscale !important; */
}
::-webkit-scrollbar {
  width: 6px !important;
  height: 6px !important;
}

::-webkit-scrollbar-track {
  background-color: transparent !important;
}

::-webkit-scrollbar-thumb {
  background: #E5EBEC;
  border-radius: 16px;
}
</style>
