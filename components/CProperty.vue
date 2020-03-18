<template>
  <div class="p-container">
    <v-card v-if="pdata" class="p-card">
      <v-card-title
        class="card-title py-3"
        v-if="pdata.address"
      >{{pdata.address.property_name}}, {{pdata.address.road_name}}</v-card-title>
      <v-card-text class="pb-3">
        <div class="availability">Available From {{pdata.start_date | moment}}</div>
        <v-layout row justify-space-between align-center class="ma-0 my-3">
          <div>
            <v-icon color="error">mdi-bed-double-outline</v-icon>
            {{pdata.room_details.length}} {{pdata.room_details.length>1?"Bedrooms" :"Bedroom"}}
          </div>
          <div>
            <v-icon color="error">mdi-hot-tub</v-icon>
            {{pdata.bathrooms}} {{pdata.bathrooms>1?'Bathrooms':'Bathroom'}}
          </div>
          <div>
            <v-icon color="error">mdi-sofa</v-icon>Living Space
          </div>
        </v-layout>
        <div>
          <strong>£{{procePerMonth}} PCM</strong> Excluding Bills
        </div>
      </v-card-text>
      <v-card-actions class="pt-0 pb-3">
        <v-spacer />
        <v-btn block color="error" nuxt :href="bookNowUrl" target="__blank">Book Now</v-btn>
      </v-card-actions>
    </v-card>
    <div :class="'swiper-container scpindex-'+pindex">
      <div class="swiper-wrapper">
        <div class="swiper-slide" v-for="(objImage, index) in imagesArray" :key="index">
          <img :src="objImage.photo" />
        </div>
      </div>
      <div :class="'swiper-pagination sppindex-'+pindex"></div>
    </div>
  </div>
</template>
<script>
import moment from "moment";
import Swiper from 'swiper';
import axios from '~/plugins/axios'
export default {
  props: ["pdata", "pindex"],
  data: () => ({
    procePerMonth: 0,
    imagesArray: [],
    bookNowUrl: ""
  }),
  filters: {
    moment: (date) => {
      return moment(date).format('MMMM Do YYYY');
    }
  },
  created() {
    if (this.pdata) {
      if (this.pdata.contracts && this.pdata.contracts.length > 0) {
        this.bookNowUrl = this.pdata.contracts[0].book_now_url;
        if (this.pdata.contracts[0].prices && this.pdata.contracts[0].prices.length > 0) {
          this.pdata.contracts[0].prices.forEach(objPrice => {
            this.procePerMonth += Number(objPrice.price_per_person_per_week) * 4;////price per month/per calendar month
          });
        }
      }
      if (this.pdata.media && this.pdata.media.photos && this.pdata.media.photos.length > 0) {
        this.imagesArray = this.pdata.media.photos;
        setTimeout(() => {
          const mySwiper = new Swiper('.scpindex-' + this.pindex, {////generate dynamic class to avoid conflicts
            pagination: {
              el: '.sppindex-' + this.pindex,
              clickable: true
            }
          });
        });

        ////changed size to load different images in slider
        this.imagesArray.forEach((objImage,index) => {
          if (objImage.photo) {
            objImage.photo=objImage.photo.replace("1280","128"+this.pindex);
            objImage.photo=objImage.photo.replace("720","72"+index);           
          }
        });
      }
    }

  }
}
</script>
<style lang="scss">
.swiper-pagination {
  background: white;
  .swiper-pagination-bullet {
    background: #ccc;
    opacity: 1;
  }
  .swiper-pagination-bullet-active {
    background: #f15b40;
  }
}

.swiper-container-horizontal {
  > .swiper-pagination-bullets {
    bottom: 0px;
  }
}
</style>
<style lang="scss" scoped>
.v-card {
  width: 400px;
  max-width: 100%;
}
.card-title {
  font-weight: normal;
  font-size: 20px;
}
.availability {
  font-size: 18px;
}
.v-icon {
  margin-right: 3px;
}
.swiper-container {
  width: 1250px;
  max-width: 90%;
  height: 330px;
  .swiper-slide {
    text-align: center;
    img {
      max-width: 100%;
      height: 100%;
    }
  }
}
.p-container {
  position: relative;
  margin-bottom: 10px;
  .p-card {
    position: absolute;
    z-index: 100;
    left: 50%;
    top: calc(50% - 11px);
    transform: translate(-50%, -50%);
  }
}
</style>
