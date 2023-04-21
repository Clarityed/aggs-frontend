<template>
  <div class="index-page">
    <a-input-search
      v-model:value="searchParams.text"
      placeholder="input search text"
      enter-button="Search"
      size="large"
      @search="onSearch"
    />
  </div>
  <MyDivider />
  <a-tabs v-model:activeKey="activeKey" @change="onTabSearch">
    <a-tab-pane key="post" tab="文章">
      <PostList :post-list="postList" />
    </a-tab-pane>
    <a-tab-pane key="picture" tab="图片" force-render>
      <PictureList :picture-list="pictureList" />
    </a-tab-pane>
    <a-tab-pane key="user" tab="用户">
      <UserList :user-list="userList" />
    </a-tab-pane>
  </a-tabs>
</template>

<script setup lang="ts">
import { ref, watchEffect } from "vue";
import PostList from "@/pages/PostList.vue";
import PictureList from "@/pages/PictureList.vue";
import UserList from "@/pages/UserList.vue";
import MyDivider from "@/pages/MyDivider.vue";
import { useRoute, useRouter } from "vue-router";
import myAxios from "@/plugins/myAxios";

/* route 取出 url 里面的参数 */
const route = useRoute();
/* router 跳转传递参数 */
const router = useRouter();
const activeKey = route.params.category;

const initSearchParams = {
  text: "",
  pageSize: 10,
  pageNum: 1,
};

const searchParams = ref(initSearchParams);

/* 理论上只要这里面监听的参数改变，就会同步刷新 */
watchEffect(() => {
  searchParams.value = {
    ...initSearchParams,
    text: route.query.text as string,
  } as never;
});

const postList = ref([]);
const userList = ref([]);
const pictureList = ref([]);

/**
 * 加载数据
 * @param params
 */
const loadDate = (params: any) => {
  /* 请求后端接口 */
  const query = {
    ...params,
    searchText: params.text,
  };
  myAxios.post("search/aggs", query).then((res: any) => {
    postList.value = res.postVOList;
    userList.value = res.userVOList;
    pictureList.value = res.pictureList;
  });
};

/* 加载页面时首先就执行一次 */
loadDate(initSearchParams);

const onSearch = (value: string) => {
  console.log(value);
  router.push({
    query: searchParams.value,
  });
  loadDate(searchParams.value);
};

const onTabSearch = (key: string) => {
  router.push({
    path: `/${key}`,
    query: searchParams.value,
  });
};
</script>
