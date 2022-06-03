<template>
  <navbar
    position="fixed"
    type="primary"
    :transparent="transparent"
    :color-on-scroll="colorOnScroll"
    menu-classes="ml-auto"
  >
    <template>
      <router-link class="navbar-brand" to="/">
        <!-- AAA -->
        <img :src="require('../../public/img/AAA_logo2.png')" style="max-height: 40px" alt="AAA"/>
      </router-link>
    </template>
    <template slot="navbar-menu">
      <drop-down
        tag="li"
        title=" Services"
        icon="now-ui-icons design_app"
        class="nav-item"
      >
        <nav-link to="/andamios">
          <i class="now-ui-icons business_chart-pie-36"></i> Andamios
        </nav-link>
        <nav-link to="/aires">
          <i class="now-ui-icons design_bullet-list-67"></i> Aires acondicionados
        </nav-link>
        <nav-link to="/pruebas">
          <i class="now-ui-icons ui-2_settings-90"></i> Pruebas
        </nav-link>
      </drop-down>
      <drop-down
              tag="li"
              title="Examples"
              icon="now-ui-icons design_image"
              class="nav-item"
      >
        <nav-link to="/landing">
          <i class="now-ui-icons education_paper"></i> Landing
        </nav-link>
        <nav-link to="/login">
          <i class="now-ui-icons users_circle-08"></i> Login
        </nav-link>
        <nav-link to="/profile">
          <i class="now-ui-icons users_single-02"></i> Profile
        </nav-link>
      </drop-down>
      <!-- <li class="nav-item">
        <a
          class="nav-link btn btn-neutral"
          href="/"
        >
          <i class="now-ui-icons arrows-1_share-66"></i>
          <p>Upgrade to PRO</p>
        </a>
      </li> -->
      <li class="nav-item">
        <a
          class="nav-link"
          rel="tooltip"
          title="Github organization"
          data-placement="bottom"
          href="https://github.com/ComputacionGrafica2022-1/"
          target="_blank"
        >
          <i class="fab fa-github"></i>
          <p class="d-lg-none d-xl-none">Github</p>
        </a>
      </li>
      <li class="nav-item">
        <a
          class="nav-link"
          rel="tooltip"
          title="vue-now-ui-kit"
          data-placement="bottom"
          href="https://www.creative-tim.com/product/vue-now-ui-kit"
          target="_blank"
        >
          <img src="img/favicon.png" style="max-height: 20px"/>
          <p class="d-lg-none d-xl-none">Guide</p>
        </a>
      </li>
    </template>
  </navbar>
</template>

<script>
import { DropDown, Navbar, NavLink } from '@/components';
import { Popover } from 'element-ui';

export default {
  name: 'main-navbar',
  props: {
    transparent: Boolean,
    colorOnScroll: Number
  },
  components: {
    DropDown,
    Navbar,
    NavLink,
    [Popover.name]: Popover
  },
  images: {
    logo: require('../../public/img/AAA_logo2.png')
  }

};
</script>

<style scoped></style>
