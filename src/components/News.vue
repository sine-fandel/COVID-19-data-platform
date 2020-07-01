<template>
<div>
  <h3>News Flash</h3>
  <el-timeline>
    <el-timeline-item
      v-for="(news, index) in newsData"
      :key="index"
      :timestamp="news.pubDateStr">
      <el-link type="primary" v-bind:href="[newsData[0].sourceUrl]" target="_blank" style="font-size: 20px">{{ news.title }}</el-link>
      <div>{{ news.summary }}</div>
      <div style="font-size: 1px; font-color:#CCCCCC;">from: {{news.infoSource}}</div>
    </el-timeline-item>
  </el-timeline>
</div>
</template>

<script>
import axios from "axios"

export default {
  name: "News",
  data () {
    return {
      newsData: [],
    }
  },
  mounted () {
    this.showNews ();
  },
  methods: {
    showNews () {
      axios.get ("http://api.tianapi.com/txapi/ncov/index?key=1e7d4b100fbc599b0ffc1446c333780a",).then (response => {
        if (response.data.msg == "success") {
          // console.log (response.data);
          this.newsData = response.data.newslist[0].news;
        }
      }).catch (error => {
          // console.log (error);
          alert (error);
        })
      }
  } 
}
</script>