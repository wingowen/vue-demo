<template>
  <el-row>
    姓名：{{info.name}}
    <el-input v-model="info.name" placeholder="请输入姓名"></el-input>
    年龄：{{info.age}}
    <el-input v-model="info.age" placeholder="请输入年龄"></el-input>
    性别：{{info.sex}}
    <el-select v-model="info.sex" placeholder="请选择">
      <el-option v-for="item in options" :key="item" :value="item"></el-option>
    </el-select>
    </el-select>
    <el-button @click="create">创建</el-button>
    <template>
      <el-table :data="tabledata" align="left">
        <el-table-column prop="name" label="姓名"></el-table-column>
        <el-table-column prop="age" label="年龄"></el-table-column>
        <el-table-column prop="sex" label="性别"></el-table-column>
        <el-table-column label="操作">
          <template slot-scope="de">
            <el-button size="mini" type="danger" @click="del(de.$index)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </template>
  </el-row>
</template>
<script>
  import Storage from '../store/store'
  export default {
    name: "NewContact",
    data() {
      return {
        info: {
          name: '',
          age: '',
          sex: ''
        },
        options: [
          '女', '男',
        ],
        tabledata: Storage.fetch()
      }
    },
    methods: {
      create() {
        // v-model 双向绑定：用户填写的信息自动绑定到对应的对象中
        this.tabledata.push(this.info) // 给 tabledata 添加一个对象（之前我们创建的 info ）
        this.info = {name: '', age: '', sex: ''} // 点击创建后，让option还原，而不是停留在所选的项
      },
      del(index) {
        this.tabledata.splice(index, 1) //删除点击的对象，index是lot-scope="a" a.$index传过来的
      }
    },
    watch:{
      tabledata:{
        handler(items){Storage.save(items)},
        deep: true
      }
    }
  }
</script>
<style>
  #main{
    float: none;
    margin: 0 auto;
  }
  .el-input{
    padding-bottom: 5px;
  }
  .el-select {
    display: block;
  }
  .btn-auto{
    width: 100%;
    margin-top: 12px;
  }
</style>
