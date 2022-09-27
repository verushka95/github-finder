<template>
<div class="wrapper">
  <header>
    <div class="navbar">
      <div class="container">
        <div class="navbar-content">
          <div class="logo">Github Finder</div>
          <!-- <ul class="navbar-list">
            <li class="navbar-item" v-for="link in links" :key="link.title">
              <router-link class="navbar-link" :title="link.title" :to="link.url">{{ link.title }}</router-link>
            </li>
          </ul> -->
        </div>
      </div>
    </div>
    <h1>HEADER</h1>
  </header>

  <router-view></router-view>

  <footer>

  </footer>
</div>
  
</template>

<script>
export default {
  data(){
    return {
      links: [
        {
          title: 'Home',
          url: '/'
        },
        {
          title: 'Example',
          url: '/example'
        },
      ]
    }
  }
}
</script>

<style lang="scss">
.navbar-link{
  &.router-link-exact-active{
    color: #000;
    font-weight: bold;
  }
}
</style>
