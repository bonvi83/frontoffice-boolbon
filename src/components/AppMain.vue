<script>
import axios from "axios";
import AppMainHero from "./AppMainHero.vue";

export default {
  data() {
    return {
      apartments: [],
      apartmentId: null,

      pagLinks: [],
      totalPage: 0,
      paginationBaseURL: "",
    };
  },

  components: { AppMainHero },

  methods: {
    fetchApartments() {
      axios.get(`http://127.0.0.1:8000/api/apartments`).then((res) => {
        this.apartments = res.data.data;
        this.pagLinks = res.data.links;
        this.totalPage = res.data.last_page;
        this.paginationBaseURL = res.config.url;
      });
    },

    paginationNav(url) {
      axios
        .get(url)
        .then((response) => {
          console.log(response.config.url);
          console.log(response.data);
          console.log(response.data.links);
          this.apartments = response.data.data;
          this.pagLinks = response.data.links;
        })
        .catch((e) => {
          console.log(e.message);
        });
    },
  },

  created() {
    this.fetchApartments();
  },
};
</script>

<template>
  <AppMainHero />

  <section class="container-fluid">
    <div class="mb-2">
      <div class="mt-5 border-bottom border-primary">
        <h2>Appartamenti in evidenza</h2>
      </div>

      <div class="row g-4 mt-3">
        <div
          v-for="apartment in apartments"
          class="col-12 col-sm-6 col-md-4 col-lg-3"
        >
          <div
            class="card h-100"
            :class="apartment.sponsorships ? 'sponsorshpis' : ''"
          >
            <router-link
              :to="{ name: 'apartment.show', params: { slug: apartment.slug } }"
            >
              <img
                :src="apartment.cover_img"
                class="card-img-top image-card"
                alt="immagine-placeholder"
              />
            </router-link>
            <div class="card-body">
              <router-link
                :to="{
                  name: 'apartment.show',
                  params: { slug: apartment.slug },
                }"
              >
                <div class="d-flex">
                  <h5 class="card-title">
                    {{ apartment.name }}
                  </h5>
                  <span
                    ><font-awesome-icon
                      v-if="apartment.sponsorships"
                      icon="star"
                      style="color: #ffd43b"
                  /></span>
                </div>
              </router-link>
              <p>
                <span class="me-1"
                  ><font-awesome-icon icon="location-dot" /></span
                >{{ apartment.address }}
              </p>
              <router-link
                :to="{
                  name: 'apartment.show',
                  params: { slug: apartment.slug },
                }"
              >
                <button class="btn text-badg-1">
                  Vai all'appartamento
                  <i class="fa-solid fa-circle-arrow-right"></i>
                </button>
              </router-link>
            </div>
          </div>
        </div>

        <!--  paginazione -->
        <nav
          v-if="totalPage > 1"
          aria-label="Page navigation example"
          class="mb-4"
        >
          <ul class="pagination">
            <li class="page-item" v-for="link in pagLinks">
              <a
                class="page-link"
                :class="{
                  active: link.active,
                  disabled: !link.url,
                }"
                v-html="link.label"
                href="javascript:void(0)"
                @click="paginationNav(paginationBaseURL + link.url)"
              ></a>
            </li>
          </ul>
        </nav>
      </div>
    </div>
  </section>
</template>

<style lang="scss" scoped>
.card {
  box-shadow: 1px 1px 10px -5px #000000;
  color: rgb(0, 42, 56);
  img:hover {
    opacity: 0.7;
  }

  .card-body {
    a {
      text-decoration: none;
      color: rgb(0, 42, 56);
      cursor: pointer;
    }
  }

  .card-title {
    font-weight: bold;
  }
  .text-badg-1 {
    color: rgb(228, 211, 211);
    background-color: rgb(0, 42, 56);
    opacity: 0.7;
    cursor: pointer;
    &:hover {
      opacity: 1;
    }
  }
}
.image-card {
  height: 280px;
  object-fit: cover;
}
.sponsorshpis {
  border: #f5c909;
  box-shadow: 1px 1px 10px -5px #000000, 1px 1px 10px 1px #f5c909;
}
</style>
