<template>
  <BodyFlex>
    <header>
      <Navbar />
    </header>
    <Sidebar />
    <ContentBody
      :title="categorySearched.category"
      :isNullCategory="isNullCategory"
    >
      <template #topAreaContent>
        <SearchBar @search:apiSearch="handleSearch" />
      </template>
      <template #heroAreaContent>
        <Hero />
      </template>
      <template #cardAreaContent>
        <div class="grid grid-template-col-auto-03" style="grid-gap: 1.5rem;">
          <LoadingEffect v-if="isLoading" />
          <a
            class="text-white text-decoration-none"
            :href="api.Link"
            target="_blank"
            v-for="api in apiSearched"
            :key="api"
          >
            <Card
              :title="api.API"
              :subtitle="api.Category"
              :body="api.Description"
              :cors="api.Cors"
              :https="api.HTTPS"
              :auth="api.Auth"
              :faviconSrc="api.Link"
            />
          </a>
        </div>
      </template>
      <template #footerArea>
        <Footer />
      </template>
    </ContentBody>
  </BodyFlex>
</template>

<script setup>
import { onMounted, reactive, ref, onBeforeUpdate, inject } from "vue";
import getApiData from "@/components/api/categoriesApi.js";
import LoadingEffect from "@/components/LoadingEffect.vue";
import ContentBody from "@/layouts/ContentBody.vue";
import SearchBar from "@/components/SearchBar.vue";
import { useRoute, useRouter } from "vue-router";
import Sidebar from "@/components/Sidebar.vue";
import BodyFlex from "@/layouts/BodyFlex.vue";
import Footer from "@/components/Footer.vue";
import Navbar from "@/components/Navbar.vue";
import Card from "@/components/Card.vue";
import Hero from "@/components/Hero.vue";

const categoriesAttributes = inject("categoryMapping");
const route = useRoute();
const router = useRouter();
const isNullCategory = ref(null);
const apiData = ref(null);

onMounted(async () => {
  apiData.value = await getApiData(route.params.category);
  isNullCategory.value ? apiData.value.length === 0 : false;
  isLoading.value = false;
});

onBeforeUpdate(async () => {
  apiData.value = await getApiData(route.params.category);
  isNullCategory.value ? apiData.value.length === 0 : false;
  isLoading.value = false;
});

const categoryExist = categoriesAttributes.some(
  (category) => category.name === route.params.category
);

if (!categoryExist) {
  router.push("/error404");
}

let apiSearched = ref(null);
let categorySearched = reactive({
  category: null,
});
let isLoading = ref(true);

const handleSearch = (val, title) => {
  if (title === undefined) {
    categorySearched.category = route.params.category.toUpperCase();
    apiSearched.value = apiData.value;
  } else if (val.length > 0) {
    categorySearched.category = title.value.toUpperCase();
    apiSearched.value = val;
  } else {
    categorySearched.category = "NO MATCH";
    apiSearched.value = apiData.value;
  }
};
</script>
