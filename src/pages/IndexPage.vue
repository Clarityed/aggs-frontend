<template>
  <div class="index-page">
    <a-input-search
      v-model:value="searchText"
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
import { message } from "ant-design-vue";

/* route 取出 url 里面的参数 */
const route = useRoute();
/* router 跳转传递参数 */
const router = useRouter();
const activeKey = route.params.category;
const searchText = ref(route.query.text || "");

const initSearchParams = {
  type: activeKey,
  text: "",
  pageSize: 10,
  pageNum: 1,
};

const searchParams = ref(initSearchParams);

const postList = ref([]);
const userList = ref([]);
const pictureList = ref([]);

/**
 * 加载单类型数据
 * @param params
 */
const loadDate = (params: any) => {
  const { type } = params;
  if (!type) {
    message.error("类别为空");
    return;
  }
  /* 请求后端接口 */
  const query = {
    ...params,
    searchText: params.text,
  };
  myAxios.post("search/aggs", query).then((res: any) => {
    if (type === "post") {
      postList.value = res.dataList;
    } else if (type === "user") {
      userList.value = res.dataList;
    } else if (type === "picture") {
      pictureList.value = res.dataList;
    }
  });
};

/* 理论上只要这里面监听的参数改变，就会同步刷新 */
watchEffect(() => {
  searchParams.value = {
    ...initSearchParams,
    text: route.query.text,
    type: route.params.category,
  } as never;
  loadDate(searchParams.value);
});

const onSearch = (value: string) => {
  router.push({
    query: {
      ...searchParams.value,
      text: value,
    },
  });
};

const onTabSearch = (key: string) => {
  router.push({
    path: `/${key}`,
    query: searchParams.value,
  });
};
</script>
