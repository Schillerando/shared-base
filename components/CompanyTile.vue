<template>
  <!--eslint-disable-next-line-->
  <a :is="noLink ? 'span' : 'a'" @click="openLink" class="hover">
    <div class="sizing" :class="{ 'no-margin': noMargin }">
      <div class="card">
        <div class="image">
          <div v-if="this.picture == null && data.image == undefined" class="no-image">
            <i class="fa-solid fa-image fa-2xl"></i>
          </div>
          <img v-else-if="data.image != undefined" :src="data.image" alt="" />
          <img v-else :src="picture" alt="" />
        </div>
        <div class="row">
          <h2 class="col-9 name highlight">
            {{ data.name }}
          </h2>
          <div class="col-3">
            <CompanyBadge :verified="data.verified" :premium="data.abo == 'Business'" :self="data.alias == 'schillerando'"
              class="company-badge" />
          </div>
        </div>
        <div class="category highlight">
          {{ data.categories[0] }}
        </div>
        <div class="row">
          <div class="col-9 location">
            <i class="fa-solid fa-location-dot"></i>
            <div class="location-text">{{ data.location }}</div>
          </div>
        </div>
      </div>
    </div>
  </a>
</template>
  
<script>
import { supabase } from '@/supabase';
import router from '@/router'
import CompanyBadge from '@/shared/components/CompanyBadge.vue';

export default {
  name: 'CompanyTile',
  props: ['data', 'noMargin', 'noLink', 'linkViaEvent'],
  data() {
    return {
      picture: null,
    };
  },
  components: { CompanyBadge },
  async mounted() {
    if (this.data.image != undefined) {
      return;
    }

    if (this.data.header_picture != null) {
      const response = await supabase.storage
        .from('public/sellers-headings')
        .download(this.data.header_picture);
      if (response.data != null) this.picture = await response.data.text();
      if (response.error) console.warn(response.error);
    }
  },
  computed: {
    link() {
      return `/${this.data.alias}`;
    },
  },
  methods: {
    openLink() {
      console.log('Company tile: link via event?', this.$props.linkViaEvent)
      if (this.$props.linkViaEvent) {
        history.pushState({}, null, process.env.VUE_APP_MAIN_URL + '/' + this.data.alias)
        const event = new CustomEvent('openDetailView', { detail: this.data.alias });
        document.dispatchEvent(event);
      } else {
        router.push(this.data.alias);
      }
    }
  }
};
</script>
  
<style scoped>
.row {
  padding: 10px 0 0 10px;
  margin: 5px 0 0 5px;
}

.name {
  font-weight: 700;
  margin: 0;
  padding: 0;
  font-size: 1.5rem;
  line-height: 1.8rem;
  max-height: 5.3rem;
  overflow: hidden;
}

.image {
  width: 100%;
  position: relative;
  padding-bottom: 56.25%;
}

.no-image {
  background-color: gray;
  position: absolute;
  width: 100%;
  height: 100%;
}

img {
  position: absolute;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.sizing {
  position: relative;
  padding-bottom: calc(56.25% + 10rem);
  margin: 2.5%;
}

.no-margin {
  margin: 0;
}

.card {
  overflow: hidden;
  width: 100%;
  height: 100%;
  position: absolute;
  text-align: left;
  padding: 0;
  margin: 0;
}

.category {
  padding: 0 0 0 10px;
  margin: 0 0 0 5px;
  font-weight: 300;
}

.location {
  color: gray;
  position: absolute;
  bottom: 10px;
  margin: 0;
  padding: 0;
  display: flex;
}

.location-text {
  margin-left: 5px;
  font-weight: 400;
  font-size: 1rem;
  line-height: 1.2rem;
  max-height: 1.2rem;
  overflow: hidden;
}

.company-badge {
  float: right;
  margin-right: 5px;
}

.fa-image {
  position: absolute;
  font-size: 6rem;
  top: 50%;
  left: calc(50% - 3rem);
  color: black;
}
</style>
  
