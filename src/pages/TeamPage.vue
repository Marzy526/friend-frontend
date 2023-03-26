<template>
  <div id="teamPage">
    <van-search v-model="searchText" placeholder="搜索队伍" @search="onSearch"/>
    <van-tabs v-model:active="active" @change="onTabChange">
      <van-tab title="公开" name="public"/>
      <van-tab title="加密" name="private"/>
    </van-tabs>
    <div style="margin-bottom: 16px"/>
    <van-button class="add-button" type="primary" icon="plus" @click="toAddTeam"/>
    <team-card-list :teamList="teamList"/>
    <van-empty v-if="teamList?.length < 1" description="数据为空"/>
  </div>
</template>

<script setup lang="ts">

import {useRouter} from "vue-router";
import TeamCardList from "../components/TeamCardList.vue";
import {onMounted, ref, watchEffect} from "vue";
import myAxios from "../plugins/myAxios";
import {Toast} from "vant";

const router = useRouter();
// 跳转到创建队伍页
const toAddTeam = () => {
  router.push({
    path: "/team/add"
  })
}

const active = ref('public')  // 默认初值公开页
const searchText = ref('');   // 队伍搜索词
/**
 * 切换查询状态
 * @param name
 */
const onTabChange = (name = '') => {
  if (name === 'public') {  // 查公开
    listTeam(searchText.value, 0);
  } else {  // 查加密
    listTeam(searchText.value, 2);
  }
}
// 搜索指定队伍
const onSearch = (val = '') => {
  listTeam(val);
};

// 队伍列表
const teamList = ref([]);
/**
 * 搜索队伍
 * @param val 搜索内容
 * @param status  队伍状态
 * @returns {Promise<void>}
 */
const listTeam = async (val = '', status = 0) => {
  // 接收后端查到的数据
  const res = await myAxios.get("/team/list", {
    params: {
      searchText: val,
      pageNum: 1,
      status,
    },
  });
  if (res?.code === 0) {
    teamList.value = res.data;
  } else {
    teamList.value = [];
    Toast.fail('无队伍存在');
  }
}

// 该函数每次页面加载时只触发一次
onMounted(() => {
  listTeam();
})

</script>

<style scoped>
</style>
