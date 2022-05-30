<template>
  <div>
    <div class="section section-nucleo-icons">
      <div class="container">
        <div class="row">
          <div class="col-lg-6 col-md-12">
            <h2 class="title">Andamios</h2>
            <h5 class="description">
              Now UI Kit comes with 100 custom icons made by our friends from
              NucleoApp. The official package contains over 2.100 thin icons
              which are looking great in combination with Now UI Kit Make sure
              you check all of them and use those that you like the most.
            </h5>
            <!-- <div class="nucleo-container">
                    <img v-lazy="'assetsimg/nucleo.svg'" alt="">
                </div> -->
            <router-link to="/andamios">
                <a class="btn btn-primary btn-round btn-lg">Comenzar</a>
            </router-link>
          </div>
          <div class="col-lg-6 col-md-12">
            <img src="img/andamios.png" />
          </div>
        </div>
      </div>
    </div>
    <div class="section section-nucleo-icons">
      <div class="container">
        <div class="row">
          <div class="col-lg-6 col-md-12">
            <img src="img/aireAcondicionado.jpeg" />
          </div>
          <div class="col-lg-6 col-md-12 text-lg-right">
            <h2 class="title">Aires acondicionados</h2>
            <h5 class="description">
              Now UI Kit comes with 100 custom icons made by our friends from
              NucleoApp. The official package contains over 2.100 thin icons
              which are looking great in combination with Now UI Kit Make sure
              you check all of them and use those that you like the most.
            </h5>
            <!-- <div class="nucleo-container">
                    <img v-lazy="'assetsimg/nucleo.svg'" alt="">
                </div> -->
            <router-link to="/aires">
                <a class="btn btn-primary btn-round btn-lg">Comenzar</a>
            </router-link>
            <!-- <a
            href="https://nucleoapp.com/?ref=1712"
            class="btn btn-primary btn-simple btn-round btn-lg"
            target="_blank"
            >View All Icons</a
          > -->
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { NavLink } from "@/components";

export default {
  components: {
    // NavLink
  },
};
</script>

<style scoped>
/* nav-link:hover{
     
 } */
</style>>

