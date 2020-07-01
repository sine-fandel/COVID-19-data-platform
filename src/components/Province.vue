<template>
<div>
  <el-table :data="city" style="width: 100%" @row-click="showDetails" ref="detailsTable">
    <el-table-column type="expand">
      <template>
        <el-form label-position="left" inline class="demo-table-expand">
          <el-form-item>
            <city :weather="weather"></city>
          </el-form-item>
        </el-form>
      </template>
    </el-table-column>
    <el-table-column
      prop="name"
      width="120">
    </el-table-column>
    <el-table-column
      prop="conadd"
      width="140">
    </el-table-column>
    <el-table-column
      prop="econNum"
      width="160">
    </el-table-column>
    <el-table-column
      prop="conNum"
      width="150">
    </el-table-column>
    <el-table-column
      prop="cureNum"
      width="160">
    </el-table-column>
    <el-table-column
      prop="deathNum"
      width="160">
    </el-table-column>
  </el-table>
</div>
</template>

<script>
import jsonp from 'jsonp'
import city from "../components/City"
// import axios from "axios"

export default {
  name: "Province",
  props: {
    city: Array,
  },
  data () {
    return {
      weather: [],
      date: ["2020-01-20", "2020-01-21", "2020-01-22", "2020-01-23", "2020-01-24", "2020-01-25", "2020-01-26", "2020-01-27", "2020-01-28", "2020-01-29", "2020-01-30", "2020-01-31", "2020-02-01", "2020-02-02", "2020-02-03", "2020-02-04", "2020-02-05", "2020-02-06", "2020-02-07", "2020-02-08", "2020-02-09", "2020-02-10", "2020-02-11", "2020-02-12", "2020-02-13", "2020-02-14", "2020-02-15", "2020-02-16", "2020-02-17", "2020-02-18", "2020-02-19", "2020-02-20", "2020-02-21", "2020-02-22", "2020-02-23", "2020-02-24", "2020-02-26", "2020-02-27", "2020-02-28", "2020-02-29", "2020-03-01", "2020-03-02", "2020-03-03", "2020-03-04", "2020-03-05", "2020-03-06", "2020-03-07", "2020-03-08", "2020-03-09", "2020-03-10", "2020-03-11", "2020-03-12", "2020-03-13", "2020-03-14", "2020-03-15", "2020-03-16", "2020-03-17", "2020-03-18", "2020-03-19", "2020-03-20", "2020-03-21", "2020-03-22", "2020-03-23", "2020-03-24", "2020-03-25", "2020-03-26", "2020-03-27", "2020-03-28", "2020-03-29", "2020-03-30", "2020-03-31", "2020-04-01", "2020-04-02", "2020-04-03", "2020-04-04", "2020-04-05", "2020-04-06", "2020-04-07", "2020-04-08", "2020-04-09", "2020-04-10"],
      expand: false,
      initSuccess: 0,
    }
  },
  mounted () {
    // this.showDetails ();     
  },
  methods: {
    showDetails (row) {
      let city_go = row.name;
      this.expand = !this.expand;
      // this.$refs.detailsTable.toggleRowExpansion (row, this.expand);
      this.$refs.detailsTable.toggleRowExpansion(row, this.expand);
      console.log (city_go);
      for (let i = 0; i < this.date.length; i ++) {
        jsonp ("https://api.binstd.com/weather2/query?appkey=d6c9811189f7d683&city=" + city_go + "&date=" + this.date[i],
          {},
          (err, data) => {
            if (!err) {
              // console.log (data.result.temphigh);
              this.weather.push (data.result.temphigh);
              console.log (this.weather[i]);
            }
            else {
              console.log (111);
            }
          }
        )
      }

      
      // console.log (city);
      // this.weather = [3, 5, 2, 10]
      // console.log (this.weather);
    }
  },
  components: {
    city,

  }
}
</script>