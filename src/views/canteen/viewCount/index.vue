<template>
  <div class="app-container">
    <el-divider content-position="left">最近一周预约人数</el-divider>
    <el-table
      v-loading="listLoading"
      :data="latestList"
      border
      fit
      highlight-current-row
      style="width: 100%"
    >
      <el-table-column label="日期" align="center">
        <template slot-scope="{ row }">
          <span>{{ row.date | parseTime('{y}-{m}-{d}') }}</span>
        </template>
      </el-table-column>
      <el-table-column label="人数" align="center">
        <template slot-scope="{ row }">
          <span>{{ row.count }}</span>
        </template>
      </el-table-column>
    </el-table>
    <el-divider content-position="left">预约记录查询</el-divider>
    <el-table
      v-loading="listLoading"
      :data="mealOrderList"
      border
      fit
      highlight-current-row
      style="width: 100%"
    >
      <el-table-column label="日期" align="center">
        <template slot-scope="{ row }">
          <span>{{ row.odate | parseTime('{y}-{m}-{d}') }}</span>
        </template>
      </el-table-column>
      <el-table-column label="姓名" align="center">
        <template slot-scope="{ row }">
          <span>{{ row.realName }}</span>
        </template>
      </el-table-column>

      <el-table-column label="电话" align="center">
        <template slot-scope="{ row }">
          <span>{{ row.phone }}</span>
        </template>
      </el-table-column>

      <el-table-column label="时间段" align="center">
        <template slot-scope="{ row }">
          <span>{{
            row.operiod === 0 ? '早餐' : row.operiod === 1 ? '午餐' : '晚餐'
          }}</span>
        </template>
      </el-table-column>
    </el-table>

    <pagination
      v-show="total > 0"
      :total="total"
      :page.sync="listQuery.pageNum"
      :limit.sync="listQuery.pageSize"
      @pagination="getOrderList"
    />
  </div>
</template>

<script>
import { latestWeekList, orderList } from '@/api/canteen'
import Pagination from '@/components/Pagination'

export default {
  name: 'ViewCount',
  components: { Pagination },
  data() {
    return {
      latestList: null,
      mealOrderList: null,
      listLoading: true,
      listQuery: {
        pageNum: 1,
        pageSize: 20
      },
      total: 0
    }
  },
  created() {
    this.getLatestWeekList()
    this.getOrderList()
  },
  methods: {
    // 获取最近七天的预约人数
    getLatestWeekList() {
      this.listLoading = true
      latestWeekList().then(response => {
        const {
          data: { data }
        } = response
        this.latestList = data
        setTimeout(() => {
          this.listLoading = false
        }, 1.5 * 1000)
      })
    },

    // 获取预约记录
    getOrderList() {
      this.listLoading = true
      orderList(this.listQuery).then(response => {
        const {
          data: {
            data: { list, total }
          }
        } = response
        this.mealOrderList = list
        this.total = total
        setTimeout(() => {
          this.listLoading = false
        }, 1.5 * 1000)
      })
    }
  }
}
</script>
