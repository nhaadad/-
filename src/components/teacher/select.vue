﻿<template>
  <div>
    <!-- 导航-->
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/teacher/welcome' }"
        >首页</el-breadcrumb-item
      >
      <el-breadcrumb-item>教学相关</el-breadcrumb-item>
      <el-breadcrumb-item>课程班级成绩查询</el-breadcrumb-item>
    </el-breadcrumb>

    <el-card>
      <!-- 搜索区域 -->
      <el-row :gutter="20">
        <el-col :span="3">
          <el-select v-model="selected" class="select">
            <el-option
              v-for="item in options"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            >
            </el-option>
          </el-select>
        </el-col>
        <el-col :span="5">
          <el-input
            placeholder="请输入课程号"
            prefix-icon="el-icon-search"
            v-model="input1"
            clearable
            @clear="getstudentscore"
          >
          </el-input>
        </el-col>
        <el-col :span="5">
          <el-input
            placeholder="请输入教师号"
            prefix-icon="el-icon-search"
            v-model="input2"
            clearable
            @clear="getstudentscore"
          >
          </el-input>
        </el-col>
        <el-col :span="4">
          <el-button @click="getstudentscore" type="primary"> 搜索 </el-button>
        </el-col>
      </el-row>
      <!--学生成绩列表区域-->
      <el-table :data="scorelistShow" border stripe :cell-style="cellStyle">
        <!-- <el-table-column
          :label="this.selected + '   学期学生成绩单'"
          align="center"
        >
        </el-table-column> -->
        <el-table-column type="index"></el-table-column>
        <el-table-column
          label="学号"
          prop="student.user.user_id"
          align="center"
        ></el-table-column>
        <el-table-column
          label="姓名"
          prop="student.user.user_name"
          align="center"
        ></el-table-column>
        <el-table-column
          label="课程号"
          prop="open.course.course_id"
          align="center"
        ></el-table-column>
        <el-table-column
          label="课程名"
          prop="open.course.course_name"
          align="center"
        ></el-table-column>
        <el-table-column
          label="教师号"
          prop="open.teacher.user.user_id"
          align="center"
        ></el-table-column>
        <el-table-column
          label="教师名"
          prop="open.teacher.user.user_name"
          align="center"
        ></el-table-column>
        <el-table-column
          label="学期"
          prop="open.semester"
          align="center"
        ></el-table-column>
        <el-table-column
          label="成绩"
          prop="score"
          align="center"
        ></el-table-column>
      </el-table>
      <!--页码栏-->
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="currentPage"
        :page-sizes="[1, 2, 3, 5]"
        :page-size="pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
      >
      </el-pagination>
    </el-card>
  </div>
</template>

<script>
// import html2canvas from 'html2canvas'
// import printJS from 'print-js'
export default {
  data() {
    return {
      options: [
        {
          label: '--请选择--',
          value: '',
        },
        {
          value: '202201',
          label: '202201',
        },
        {
          value: '202202',
          label: '202202',
        },
        {
          value: '202203',
          label: '202203',
        },
        {
          value: '202204',
          label: '202204',
        },
      ],
      selected: '', // 搜索选择
      input1: '',
      input2: '1001',
      scorelist: [],
      scorelistShow: [],
      total: 0, // 列表总数
      currentPage: 1, // 当前页面
      pageSize: 3, // 每页展示列表数
    }
  },
  created() {
    this.getstudentscore()
  },
  methods: {
    async getstudentscore() {
      var query = {}
      if (this.input2) {
        query.teacher = this.input2
      }
      if (this.input1) {
        query.course = this.input1
      }
      if (this.selected) {
        query.semester = this.selected
      }
      const { data: res } = await this.$http.get('teacher/score/search/', {
        params: query,
      })
      if (res.status !== 200) return this.$message.error('获取学生成绩列表失败')
      this.scorelist = res.data.Scores
      this.total = res.data.total
      this.currentPage = 1
      var start = 0
      var end = start + this.pageSize
      this.scorelistShow = res.data.Scores.slice(start, end)
      //   console.log(res.data.Scores[0].open.course.course_name)
      //   console.log(this.selected)
      // console.log(res.data.Scores[0].open.course.course_name)
      //   console.log(res)
    },
    handleSizeChange(pageSize) {
      //   console.log(pageSize)
      this.pageSize = pageSize
      let start = 0
      let end = start + pageSize
      this.scorelistShow = this.scorelist.slice(start, end)
    },
    // 监听当前页面变化
    handleCurrentChange(currentPage) {
      this.currentPage = currentPage
      let start = (currentPage - 1) * this.pageSize
      let end = start + this.pageSize
      this.scorelistShow = this.scorelist.slice(start, end)
    },
    cellStyle(row, column, rowIndex, columnIndex) {
      if (row.column.label == '成绩' && row.row.score < 60) {
        return 'color:#FF6930'
      }
    },
  },
}
</script>

<style lang="less" scoped>
.demo-input-label {
  display: inline-block;
  width: 130px;
}
</style>
