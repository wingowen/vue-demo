<template>
  <!-- 路由标签 -->
  <router-link to="newcontact"><h1>{{ msg }}</h1></router-link>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Rout to NewContact'
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
