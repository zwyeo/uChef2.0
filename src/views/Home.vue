<template>
  <div class="container-fluid px-5">
    <nav-bar></nav-bar>
  </div>
  <div class="banner">
    <banner></banner>
  </div>
  <div id="popular-recipe" class="container-fluid">
    <!-- RECOMMENDED FOR YOU SECTION -->
    <div v-if="recommendations_required == true">
      <h2 class="text-center p-5">Recommended For You</h2>
      <div class="row d-flex justify-content-center">
        <div
          v-for="recipe in recommendations_list"
          :key="recipe.id"
          class="col-xl-4 col-md-6"
        >
          <router-link
            :to="{ name: 'recipe-details', params: { id: recipe.idMeal } }"
          >
            <recipe-card
              :title="recipe.strMeal"
              :img="recipe.strMealThumb"
              :id="recipe.idMeal"
              class="mb-5"
            >
            </recipe-card>
          </router-link>
        </div>
      </div>
    </div>

    <!-- POPULAR RECIPE SECTION -->
    <h2 class="text-center p-5">Recommended By uChef</h2>
    <div class="row d-flex justify-content-center">
      <div
        v-for="recipe in $store.state.popularRecipe"
        :key="recipe.id"
        class="col-xl-4 col-md-6"
      >
        <router-link
          :to="{ name: 'recipe-details', params: { id: recipe.id } }"
        >
          <recipe-card
            :title="recipe.title"
            :img="recipe.img"
            :id="recipe.id"
            class="mb-5"
          ></recipe-card>
        </router-link>
      </div>
    </div>

    <!-- COMMUNITY RECIPE SECTION -->
    <h2 class="text-center p-5">Our Community Recipes</h2>
    <div class="row d-flex justify-content-center">
      <div
        v-for="recipe in $store.state.communityRecipe"
        :key="recipe.id"
        class="col-xl-4 col-md-6"
      >
        <router-link
          :to="{
            name: 'recipe-details',
            params: { id: recipe.id },
          }"
        >
          <recipe-card
            :title="recipe.title"
            :img="recipe.image"
            :id="recipe.id"
            class="mb-5"
          ></recipe-card>
        </router-link>
      </div>
    </div>
  </div>
</template>

<script>
import NavBar from "../components/NavBar.vue";
import RecipeCard from "@/components/RecipeCard.vue";
import Banner from "@/components/Banner.vue";
import axios from "axios";

export default {
  name: "Home",
  components: {
    NavBar,
    RecipeCard,
    Banner,
  },
  data() {
    return {
      recommendations_required: false,
      recommendations_list: [],
    };
  },
  created() {
    // This will refresh the popular and community recipe list from DB
    this.populatePopularRecipe();
    this.populateCommunityRecipe();
    // console.log(this.$store.state.userName);
    this.$store.state.queryParam = "";
    this.$store.state.setsearch = "professional";
    if (this.$store.state.prevRouteName == "register") {
      this.$store.state.prevRouteName = "";
      location.reload();
    }

    // Get recommendations for logged in users
    if (this.$store.state.userId !== "") {
      var review_list = [];
      axios
        .get(
          `https://wad-proj-22042-default-rtdb.asia-southeast1.firebasedatabase.app/users/${this.$store.state.userId}/preferences.json`
        )
        .then((response) => {
          var obj = response.data;
          // console.log(Object.values(obj)[0])
          var reviews = Object.values(obj)[0];
          for (let review in reviews) {
            review_list.push([review, Number(reviews[review])]);
          }
          // console.log(review_list);
          //sort according
          review_list.sort(function (a, b) {
            return b[1] - a[1];
          });
          if (review_list[0][1] > 0) {
            this.recommendations_required = true;
            // console.log(review_list[1][0])
            // console.log(review_list[2][0])
            //get recipe data from meal db for top 3 categories

            //top category
            var url = "https://themealdb.com/api/json/v1/1/filter.php?";
            axios
              .get(url, {
                params: {
                  c: review_list[0][0],
                },
              })
              .then((response) => {
                var objects = response.data.meals;
                this.recommendations_list.push(
                  objects[0],
                  objects[1],
                  objects[2]
                );
                // console.log(this.recommendations_list)

                //second category
                axios
                  .get(url, {
                    params: {
                      c: review_list[1][0],
                    },
                  })
                  .then((response) => {
                    var objects = response.data.meals;
                    this.recommendations_list.push(objects[0], objects[1]);

                    //third category
                    axios
                      .get(url, {
                        params: {
                          c: review_list[2][0],
                        },
                      })
                      .then((response) => {
                        var objects = response.data.meals;
                        this.recommendations_list.push(objects[0]);
                        // console.log(this.recommendations_list)
                      });
                  });
              });
          }
        });
    }

    console.log(this.recommendations_list);
  },
  methods: {
    populatePopularRecipe() {
      this.$store.dispatch("setPopularRecipe");
    },
    populateCommunityRecipe() {
      this.$store.dispatch("setCommunityRecipe");
      // console.log(this.$store.sta2te.communityRecipe);
    },
  },
};
</script>
<style scoped>
/* remove the underline in recipe card */
a {
  text-decoration: none;
}

.recipe-card-style {
  padding-left: 5em;
  padding-right: 5em;
}

/* Start */

@media (max-width: 768px) {
  .recipe-card-style {
    padding: 0;
  }
}

@media (max-width: 767px) {
  .banner {
    display: none;
  }
}

@media only screen and (min-width: 992px) and (max-width: 1199px) {
  .hero-slides .single-hero-slide {
    height: 650px;
  }
}

@media only screen and (min-width: 768px) and (max-width: 991px) {
  .hero-slides .single-hero-slide {
    height: 650px;
  }
}

@media only screen and (max-width: 767px) {
  .hero-slides .single-hero-slide {
    height: 650px;
  }
}

.hero-slides .single-hero-slide .hero-slides-content {
  position: relative;
  z-index: 1;
  padding: 60px 50px;
  border-left: 3px solid #40ba37;
  background-color: rgba(24, 24, 24, 0.54);
}

@media only screen and (max-width: 767px) {
  .hero-slides .single-hero-slide .hero-slides-content {
    padding: 30px 20px;
  }
}

.hero-slides .single-hero-slide .hero-slides-content h2 {
  font-size: 30px;
  color: #ffffff;
  margin-bottom: 30px;
  font-weight: 600;
}

@media only screen and (max-width: 767px) {
  .hero-slides .single-hero-slide .hero-slides-content h2 {
    font-size: 24px;
  }
}

.hero-slides .single-hero-slide .hero-slides-content p {
  font-size: 16px;
  margin-bottom: 50px;
  color: #ffffff;
}

@media only screen and (min-width: 768px) and (max-width: 991px) {
  .breakpoint-on .classynav ul li.cn-dropdown-item > a::after,
  .breakpoint-on .classynav ul li.megamenu-item > a::after {
    top: 10px;
  }
}

@media only screen and (max-width: 767px) {
  .breakpoint-on .classynav ul li.cn-dropdown-item > a::after,
  .breakpoint-on .classynav ul li.megamenu-item > a::after {
    top: 10px;
  }
}

/* :: 7.0 Top Catagory Area */
.single-top-catagory {
  position: relative;
  z-index: 2;
  margin-bottom: 80px;
  -webkit-transition-duration: 500ms;
  transition-duration: 500ms;
  overflow: hidden;
}

.single-top-catagory img {
  width: 100%;
}

.single-top-catagory::after {
  content: "";
  position: absolute;
  height: 100%;
  width: 100%;
  z-index: 5;
  top: 0;
  left: 0;
  background: rgba(0, 0, 0, 0.8);
  background: -webkit-linear-gradient(to right, #000, rgba(0, 0, 0, 0.1));
  background: linear-gradient(to right, #000, rgba(0, 0, 0, 0.1));
}

.single-top-catagory .top-cta-content {
  position: absolute;
  top: 50%;
  left: 40px;
  width: auto;
  height: auto;
  -webkit-transform: translateY(-50%);
  transform: translateY(-50%);
  z-index: 10;
}

.single-top-catagory .top-cta-content h3 {
  font-size: 30px;
  color: #ffffff;
  font-weight: 600;
  margin-bottom: 5px;
}

@media only screen and (max-width: 767px) {
  .single-top-catagory .top-cta-content h3 {
    font-size: 18px;
  }
}

.single-top-catagory .top-cta-content h6 {
  color: #ffffff;
  margin-bottom: 30px;
  display: block;
  font-weight: 400;
}

@media only screen and (max-width: 767px) {
  .single-top-catagory .top-cta-content h6 {
    margin-bottom: 10px;
  }
}

/* :: 8.0 Best Receipe Area */
.best-receipe-area {
  position: relative;
  z-index: 1;
  padding-bottom: 50px;
}

.single-best-receipe-area {
  position: relative;
  z-index: 1;
}

.single-best-receipe-area img {
  border-bottom: 3px solid #40ba37;
}

.single-best-receipe-area .receipe-content {
  padding-top: 30px;
  text-align: center;
}

.single-best-receipe-area .receipe-content h5 {
  font-size: 18px;
  margin-bottom: 10px;
  font-weight: 400;
  -webkit-transition-duration: 500ms;
  transition-duration: 500ms;
}

.single-best-receipe-area .receipe-content h5:hover,
.single-best-receipe-area .receipe-content h5:focus {
  color: #40ba37;
}

.single-best-receipe-area .receipe-content .ratings i {
  font-size: 11px;
  color: #fbb710;
  display: inline-block;
  padding: 0 1px;
}

/* :: 9.0 Cta Area */
.cta-area {
  position: relative;
  z-index: 2;
  width: 100%;
  height: 675px;
  background-attachment: fixed;
}

.cta-area .cta-content h2 {
  font-size: 72px;
  margin-bottom: 20px;
  color: #ffffff;
  font-weight: 400;
}

@media only screen and (min-width: 992px) and (max-width: 1199px) {
  .cta-area .cta-content h2 {
    font-size: 48px;
  }
}

@media only screen and (min-width: 768px) and (max-width: 991px) {
  .cta-area .cta-content h2 {
    font-size: 48px;
  }
}

@media only screen and (max-width: 767px) {
  .cta-area .cta-content h2 {
    font-size: 30px;
  }
}

.cta-area .cta-content p {
  color: #ffffff;
  margin-bottom: 100px;
  font-size: 16px;
}

@media only screen and (max-width: 767px) {
  .cta-area .cta-content p {
    margin-bottom: 50px;
  }
}

/* :: 10.0 Small Receipe Area */
.small-receipe-area {
  position: relative;
  z-index: 1;
  padding-bottom: 30px;
}

.single-small-receipe-area {
  position: relative;
  z-index: 1;
  margin-bottom: 50px;
}

.single-small-receipe-area .receipe-thumb {
  -webkit-box-flex: 0;
  -ms-flex: 0 0 100px;
  flex: 0 0 100px;
  max-width: 100px;
  width: 100px;
  padding-right: 20px;
}

.single-small-receipe-area .receipe-content span {
  font-size: 12px;
  display: block;
  color: #40ba37;
}

.single-small-receipe-area .receipe-content a {
  display: block;
}

.single-small-receipe-area .receipe-content a h5 {
  color: #474747;
  font-size: 18px;
  margin-bottom: 5px;
  -webkit-transition-duration: 500ms;
  transition-duration: 500ms;
}

@media only screen and (min-width: 992px) and (max-width: 1199px) {
  .single-small-receipe-area .receipe-content a h5 {
    font-size: 16px;
  }
}

.single-small-receipe-area .receipe-content a h5:hover,
.single-small-receipe-area .receipe-content a h5:focus {
  color: #40ba37;
}

.single-small-receipe-area .receipe-content .ratings i {
  font-size: 11px;
  color: #fbb710;
  display: inline-block;
  padding: 0 1px;
}

.single-small-receipe-area .receipe-content p {
  font-size: 12px;
  margin-bottom: 0;
  color: #9b9b9b;
}

/* :: 11.0 Quote Area */
.quote-area {
  position: relative;
  z-index: 1;
  border: 10px solid #40ba37;
  padding: 40px 25px;
  margin-bottom: 80px;
}

.quote-area span {
  font-size: 48px;
  font-style: italic;
  margin-bottom: 30px;
  display: block;
  color: #40ba37;
  line-height: 1;
}

.quote-area h4 {
  font-size: 24px;
  line-height: 1.5;
  font-style: italic;
  margin-bottom: 40px;
  color: #474747;
}

@media only screen and (min-width: 992px) and (max-width: 1199px) {
  .quote-area h4 {
    font-size: 20px;
  }
}

.quote-area p {
  color: #474747;
  font-size: 16px;
  margin-bottom: 50px;
}

.quote-area .date-comments .date {
  font-size: 12px;
  color: #40ba37;
}

.quote-area .date-comments .comments {
  font-size: 12px;
  color: #9b9b9b;
}

/* :: 12.0 Newsletter Area */
.newsletter-area {
  position: relative;
  z-index: 1;
  margin-bottom: 80px;
}

.newsletter-area h4 {
  font-size: 24px;
  margin-bottom: 50px;
  display: block;
  color: #474747;
}

@media only screen and (min-width: 992px) and (max-width: 1199px) {
  .newsletter-area h4 {
    font-size: 20px;
  }
}

.newsletter-form {
  padding: 30px 15px 100px;
}

.newsletter-form form input {
  width: 100%;
  height: 60px;
  border: none;
  background-color: #ffffff;
  color: #9b9b9b;
  font-size: 12px;
  font-style: italic;
  border-left: 3px solid #1c8314;
  padding: 0 20px;
  margin-bottom: 25px;
}

.newsletter-form p {
  margin-top: 30px;
  color: #ffffff;
  font-size: 12px;
  margin-bottom: 0;
  font-style: italic;
}

/* :: 13.0 Delicious Adds */
.delicious-add {
  position: relative;
  z-index: 1;
  margin-bottom: 80px;
}

.delicious-add img {
  width: 100%;
}

/* :: 14.0 Blog Area */
@media only screen and (min-width: 768px) and (max-width: 991px) {
  .blog-sidebar-area {
    margin-top: 80px;
  }
}

@media only screen and (max-width: 767px) {
  .blog-sidebar-area {
    margin-top: 80px;
  }
}

.single-blog-area {
  position: relative;
  z-index: 1;
}

.single-blog-area .blog-thumbnail {
  position: relative;
  z-index: 1;
}

.single-blog-area .blog-thumbnail .post-date {
  width: 75px;
  height: 100px;
  background-color: #40ba37;
  position: absolute;
  top: 20px;
  left: 20px;
  z-index: 10;
  -webkit-transition-duration: 500ms;
  transition-duration: 500ms;
  text-align: center;
}

@media only screen and (max-width: 767px) {
  .single-blog-area .blog-thumbnail .post-date {
    top: 10px;
    left: 10px;
  }
}

.single-blog-area .blog-thumbnail .post-date a {
  color: #ffffff;
  padding: 18px 10px;
  display: block;
  line-height: 1.1;
  font-size: 14px;
  font-weight: 400;
}

.single-blog-area .blog-thumbnail .post-date a span {
  font-size: 30px;
  font-weight: 700;
  display: block;
}

.single-blog-area .blog-thumbnail .post-date:hover,
.single-blog-area .blog-thumbnail .post-date:focus {
  background-color: #1c8314;
}

.single-blog-area .blog-content {
  position: relative;
  z-index: 1;
  padding-top: 40px;
}

.single-blog-area .blog-content .post-title {
  font-size: 22px;
  color: #474747;
  margin-bottom: 10px;
}

.single-blog-area .blog-content .post-title:hover,
.single-blog-area .blog-content .post-title:focus {
  color: #40ba37;
}

.single-blog-area .blog-content .meta-data {
  font-size: 12px;
  color: #a4a4a4;
  margin-bottom: 20px;
}

.single-blog-area .blog-content .meta-data a {
  text-decoration: underline;
  font-size: 12px;
  color: #a4a4a4;
}

.single-blog-area .blog-content .meta-data a:hover,
.single-blog-area .blog-content .meta-data a:focus {
  color: #40ba37;
}

.single-widget {
  position: relative;
  z-index: 1;
}

.single-widget h6 {
  margin-bottom: 50px;
  display: block;
}

.single-widget .list {
  padding-left: 30px;
}

.single-widget .list li a {
  display: block;
  padding: 5px 0;
  color: #797979;
}

.single-widget .list li a:hover,
.single-widget .list li a:focus {
  color: #40ba37;
}

.pagination {
  position: relative;
  z-index: 1;
}

.pagination .page-link {
  padding: 0;
  font-size: 16px;
  margin-right: 5px;
  border: none;
  color: #474747;
}

.pagination .page-link:hover,
.pagination .page-link:focus {
  background-color: transparent;
  color: #40ba37;
}

.pagination .page-item.active .page-link {
  background-color: transparent;
  color: #40ba37;
}

/* :: 15.0 Receipe Area */
.receipe-post-search {
  position: relative;
  z-index: 20;
}

.receipe-post-search form input {
  width: 100%;
  height: 60px;
  background-color: #f3f5f8;
  font-size: 14px;
  padding: 0 20px;
  border: none;
}

@media only screen and (min-width: 768px) and (max-width: 991px) {
  .receipe-post-search .col-12 {
    margin-bottom: 30px;
  }
}

@media only screen and (max-width: 767px) {
  .receipe-post-search .col-12 {
    margin-bottom: 15px;
  }
}

.receipe-slider {
  position: relative;
  z-index: 1;
}

.receipe-headline span {
  font-size: 14px;
  display: block;
  color: #a4a4a4;
  margin-bottom: 0;
}

.receipe-headline h2 {
  font-size: 36px;
  color: #474747;
  margin-bottom: 30px;
}

@media only screen and (max-width: 767px) {
  .receipe-headline h2 {
    font-size: 24px;
  }
}

.receipe-headline .receipe-duration {
  border-left: 3px solid #40ba37;
  padding: 15px;
}

.receipe-headline .receipe-duration h6 {
  font-size: 15px;
  margin-bottom: 5px;
}

.receipe-headline .receipe-duration h6:last-child {
  margin-bottom: 0;
}

.receipe-ratings .ratings {
  margin-bottom: 30px;
}

.receipe-ratings i {
  font-size: 18px;
  color: #fbb710;
  padding: 0 3px;
}

.single-preparation-step {
  position: relative;
  z-index: 1;
  margin-bottom: 50px;
}

.single-preparation-step h4 {
  color: #474747;
  -webkit-box-flex: 0;
  -ms-flex: 0 0 60px;
  flex: 0 0 60px;
  max-width: 60px;
  width: 60px;
  margin-bottom: 0;
}

@media only screen and (min-width: 768px) and (max-width: 991px) {
  .ingredients {
    margin-bottom: 80px;
  }
}

@media only screen and (max-width: 767px) {
  .ingredients {
    margin-bottom: 80px;
  }
}

.ingredients h4 {
  color: #474747;
  margin-bottom: 30px;
}

.ingredients .custom-checkbox .custom-control-label::before {
  border-radius: 0;
}

.ingredients .custom-control-label::before {
  width: 30px;
  height: 30px;
  background-color: #f3f5f8;
}

.ingredients .custom-control {
  padding-left: 2.5rem;
  margin-bottom: 30px;
  min-height: 35px;
}

.ingredients .custom-control-label {
  margin-bottom: 0;
  padding-top: 5px;
  font-size: 16px;
  font-weight: 600;
  color: #2f2f2f;
}

.ingredients .custom-control-label::after {
  top: 10px;
  left: 5px;
  width: 20px;
  height: 20px;
}

.ingredients
  .custom-checkbox
  .custom-control-input:checked
  ~ .custom-control-label::before {
  background-color: #40ba37;
}

/* :: 16.0 Footer Area CSS */
.footer-area {
  position: relative;
  z-index: 1;
  width: 100%;
  height: 140px;
  background-color: #ffffff;
}

@media only screen and (max-width: 767px) {
  .footer-area {
    height: 150px;
    border-top: 1px solid #ebebeb;
    margin-top: 30px;
  }
}

.footer-area .footer-social-info {
  padding-right: 15px;
}

.footer-area .footer-social-info a {
  display: inline-block;
  color: #b6b6b6;
  font-size: 13px;
  margin-right: 30px;
}

.footer-area .footer-social-info a:last-child {
  margin-right: 0;
}

.footer-area .footer-social-info a:hover,
.footer-area .footer-social-info a:focus {
  color: #40ba37;
}

.footer-area p {
  color: #4b4b4b;
  font-weight: 600;
  margin-bottom: 0;
  padding-left: 15px;
}

@media only screen and (max-width: 767px) {
  .footer-area p {
    padding-left: 0;
  }
}

.footer-area p a {
  color: #4b4b4b;
  font-weight: 600;
}

.footer-area p a:hover,
.footer-area p a:focus {
  color: #40ba37;
}

@media only screen and (max-width: 767px) {
  .footer-area .footer-logo {
    max-width: 90px;
  }
}

.follow-us-instagram {
  position: relative;
  z-index: 1;
  border-top: 1px solid #dcdcdc;
}

.follow-us-instagram h5 {
  font-size: 18px;
  padding: 35px 0;
  margin-bottom: 0;
}

.follow-us-instagram .insta-feeds {
  position: relative;
  z-index: 1;
}

.follow-us-instagram .insta-feeds .single-insta-feeds {
  position: relative;
  z-index: 1;
  -webkit-box-flex: 0;
  -ms-flex: 0 0 14.2857%;
  flex: 0 0 14.2857%;
  max-width: 14.2857%;
  width: 14.2857%;
  -webkit-transition-duration: 500ms;
  transition-duration: 500ms;
  overflow: hidden;
}

@media only screen and (max-width: 767px) {
  .follow-us-instagram .insta-feeds .single-insta-feeds {
    -webkit-box-flex: 0;
    -ms-flex: 0 0 25%;
    flex: 0 0 25%;
    max-width: 25%;
    width: 25%;
  }
}

@media only screen and (min-width: 576px) and (max-width: 767px) {
  .follow-us-instagram .insta-feeds .single-insta-feeds {
    -webkit-box-flex: 0;
    -ms-flex: 0 0 20%;
    flex: 0 0 20%;
    max-width: 20%;
    width: 20%;
  }
}

.follow-us-instagram .insta-feeds .single-insta-feeds img {
  width: 100%;
  -webkit-transition-duration: 500ms;
  transition-duration: 500ms;
}

.follow-us-instagram .insta-feeds .single-insta-feeds .insta-icon {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgba(64, 186, 55, 0.9);
  top: 0;
  left: 0;
  z-index: 10;
  cursor: pointer;
  opacity: 0;
  visibility: hidden;
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  -webkit-box-align: center;
  -ms-flex-align: center;
  -ms-grid-row-align: center;
  align-items: center;
  -webkit-box-pack: center;
  -ms-flex-pack: center;
  justify-content: center;
  -webkit-transition-duration: 500ms;
  transition-duration: 500ms;
}

.follow-us-instagram .insta-feeds .single-insta-feeds .insta-icon a {
  display: inline-block;
  color: #ffffff;
  font-size: 18px;
}

.follow-us-instagram .insta-feeds .single-insta-feeds:hover img {
  -webkit-transform: scale(1.2) rotate(2.5deg);
  transform: scale(1.2) rotate(2.5deg);
}

.follow-us-instagram .insta-feeds .single-insta-feeds:hover .insta-icon {
  opacity: 1;
  visibility: visible;
}

/* :: 17.0 Breadcumb Area CSS */
.breadcumb-area {
  position: relative;
  z-index: 10;
  width: 92%;
  height: 190px;
  margin-left: 4%;
  margin-right: 4%;
}

@media only screen and (max-width: 767px) {
  .breadcumb-area {
    height: 120px;
  }
}

.breadcumb-area .breadcumb-text h2 {
  font-size: 30px;
  margin-bottom: 0;
  color: #ffffff;
}

@media only screen and (max-width: 767px) {
  .breadcumb-area .breadcumb-text h2 {
    font-size: 24px;
  }
}

/* About Us Area */
.sub-heading {
  line-height: 2;
  font-size: 18px;
  text-align: center;
}

/* :: 18.0 Contact Area CSS */
.map-area {
  position: relative;
  z-index: 2;
}

.map-area #googleMap {
  width: 100%;
  height: 680px;
}

@media only screen and (min-width: 768px) and (max-width: 991px) {
  .map-area #googleMap {
    height: 500px;
  }
}

@media only screen and (max-width: 767px) {
  .map-area #googleMap {
    height: 350px;
  }
}

.single-contact-information {
  position: relative;
  z-index: 1;
}

.single-contact-information h6 {
  font-size: 14px;
  border-bottom: 2px solid #40ba37;
  display: inline-block;
  color: #40ba37;
}

.contact-form-area .form-control {
  height: 52px;
  width: 100%;
  background-color: #f3f5f8;
  font-size: 12px;
  font-style: italic;
  margin-bottom: 15px;
  border: none;
  border-left: 3px solid #f3f5f8;
  border-radius: 0;
  padding: 15px 30px;
  -webkit-transition-duration: 500ms;
  transition-duration: 500ms;
}

.contact-form-area .form-control:focus {
  border-left: 3px solid #40ba37;
  box-shadow: none;
}

.contact-form-area textarea.form-control {
  height: 200px;
  -webkit-transition-duration: 500ms;
  transition-duration: 500ms;
}

/* :: 19.0 Elements Area CSS */
.elements-title h2 {
  font-size: 30px;
  margin-bottom: 70px;
  color: #4b4b4b;
}

.single-pie-bar h6 {
  font-size: 18px;
  margin-bottom: 0;
}

.single-cool-fact {
  position: relative;
  text-align: center;
  z-index: 1;
  margin-bottom: 80px;
}

.single-cool-fact img {
  display: block;
  margin: 0 auto 10px;
}

.single-cool-fact h3 {
  font-size: 48px;
  margin-bottom: 10px;
  color: #40ba37;
  font-weight: 700;
}

.single-cool-fact h6 {
  font-size: 18px;
  margin-bottom: 0;
}

/* Accordians */
.single-accordion.panel {
  background-color: #ffffff;
  border: 0 solid transparent;
  border-radius: 4px;
  box-shadow: 0 0 0 transparent;
  margin-bottom: 15px;
}

.single-accordion:last-of-type {
  margin-bottom: 0;
}

.single-accordion h6 {
  margin-bottom: 0;
  text-transform: uppercase;
}

.single-accordion h6 a {
  border-radius: 0;
  background-color: #40ba37;
  color: #ffffff;
  border-left: 3px solid #1c8314;
  display: block;
  margin: 0;
  padding: 20px 60px 20px 20px;
  position: relative;
  font-size: 14px;
  text-transform: capitalize;
  font-weight: 600;
}

.single-accordion h6 a span {
  font-size: 10px;
  position: absolute;
  right: 20px;
  text-align: center;
  top: 23px;
}

.single-accordion h6 a span.accor-open {
  opacity: 0;
  -ms-filter: "progid:DXImageTransform.Microsoft.Alpha(Opacity=0)";
  filter: alpha(opacity=0);
}

.single-accordion h6 a.collapsed {
  background-color: #f3f5f8;
  color: #9b9b9b;
  border-left: 3px solid #40ba37;
}

.single-accordion h6 a.collapsed span.accor-close {
  opacity: 0;
  -ms-filter: "progid:DXImageTransform.Microsoft.Alpha(Opacity=0)";
  filter: alpha(opacity=0);
}

.single-accordion h6 a.collapsed span.accor-open {
  opacity: 1;
  -ms-filter: "progid:DXImageTransform.Microsoft.Alpha(Opacity=100)";
  filter: alpha(opacity=100);
}

.single-accordion .accordion-content {
  border-top: 0 solid transparent;
  box-shadow: none;
}

.single-accordion .accordion-content p {
  padding: 20px 15px 5px;
  margin-bottom: 0;
}

/* Tabs CSS*/
.delicious-tabs-content {
  position: relative;
  z-index: 1;
}

.delicious-tabs-content .nav-tabs {
  border-bottom: none;
  margin-bottom: 50px;
}

.delicious-tabs-content .nav-tabs .nav-link {
  background-color: #f3f5f8;
  color: #9b9b9b;
  border: none;
  border-left: 3px solid #40ba37;
  padding: 0 15px;
  height: 50px;
  line-height: 50px;
  margin: 0 2px;
  border-radius: 0;
  font-size: 14px;
  font-weight: 600;
  margin-bottom: 5px;
}

@media only screen and (min-width: 992px) and (max-width: 1199px) {
  .delicious-tabs-content .nav-tabs .nav-link {
    padding: 0 10px;
  }
}

@media only screen and (max-width: 767px) {
  .delicious-tabs-content .nav-tabs .nav-link {
    padding: 0 10px;
  }
}

.delicious-tabs-content .nav-tabs .nav-link.active {
  background-color: #40ba37;
  color: #ffffff;
  border-left: 3px solid #1c8314;
}

.delicious-tabs-content .tab-content h6 {
  font-size: 18px;
}

/* Features Area */
.single-feature-area {
  position: relative;
  z-index: 1;
}

.single-feature-area h5 {
  margin-bottom: 0;
  padding-left: 30px;
}
</style>
