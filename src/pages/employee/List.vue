<template>
<!--员工管理-->
    <div>
        员工管理
        <!-- 按钮 -->
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- /按钮 -->
        <!-- 表格 -->
        <el-table :data="employees">
            <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
            <el-table-column fixed="left" prop="username" label="用户名"></el-table-column>
            <el-table-column prop="realname" label="姓名"></el-table-column>
            <el-table-column prop="gender" label="性别"></el-table-column>
            <el-table-column prop="telephone" width="120" label="手机号"></el-table-column>
            <el-table-column prop="idCard" label="身份证号" width="200"></el-table-column>
            <el-table-column prop="bankCard" label="银行卡号" width="200"></el-table-column>
            <el-table-column fixed="right" label="操作">
                <!-- v-slot用于获取当前行数据 -->
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>

        </el-table>
        <!-- /表格 -->
        <!-- 分页 -->
         <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
        <!-- /分页 -->
        <!-- 模态框    冒号表示引用脚本-->
         <el-dialog :title="title" :visible.sync="visible" width="60%" >
             {{form}}
        <el-form :model="form" label-width="80px">
            <el-form-item label="用户名">
                <el-input v-model="form.username"></el-input>
            </el-form-item>
            <el-form-item label="姓名">
                <el-input v-model="form.realname"></el-input>
            </el-form-item>
            <el-form-item label="性别">
             <el-radio-group v-model="form.gender">
                    <el-radio label="男">男</el-radio>
                    <el-radio label="女">女</el-radio>
             </el-radio-group>
            </el-form-item>
            <el-form-item label="密码">
                <el-input type="password" v-model="form.password"></el-input>
            </el-form-item>
            <el-form-item label="手机号">
                <el-input v-model="form.telephone"></el-input>
            </el-form-item>
            <el-form-item label="身份证号">
                <el-input v-model="form.idCard"></el-input>
            </el-form-item>
            <el-form-item label="银行卡号">
                <el-input v-model="form.bankCard"></el-input>
            </el-form-item>
            
        </el-form>
         <span slot="footer" class="dialog-footer">
         <el-button @click="visible = false">取 消</el-button>
         <!-- @click=onclick -->
         <el-button type="primary" @click="submitHandler">确 定</el-button>
         </span>
        </el-dialog>
        <!-- /模态框 -->

    </div>
</template>
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    // 存放网页中需要调用的方法
    methods:{
        submitHandler(){
          let url="http://localhost:6677/waiter/saveOrUpdate";
          request({
          url,
          method:"POST",
          //为了告诉后台我的请求体中放的是查询字符串
          headers:{
              "Content-Type":"application/x-www-form-urlencoded"
          },
          //将this.form转换为查询字符串发送给后台
          data: querystring.stringify(this.form)
          }).then((response)=>{
              //请求结束
              this.closeModalHandler;
              //提示消息
              this.loadData();
              this.$message({
                  type:"success",
                  message:response.message
              })
              
          })
          this.visible=false
        },
        loadData(){
          let that=this
          let url = "http://localhost:6677/waiter/findAll"
          request.get(url).then(function(response){
          //将查询结果设置到employees里
          //箭头函数中的this指向外部函数的this
          //原先this是指向vue实例，通过vue实例访问该实例中的数据，methods中
          that.employees=response.data;
      })
        },
        toDeleteHandler(id){
            // 确认
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'+id
          });
        })
        
        },
        toUpdateHandler(row){
            this.title="修改员工信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
       toAddHandler(){
            this.title="添加员工信息";
           this.visible=true;
       }
    },
    //用于存放要在网页中存放的数据
    data(){
        return {
            title:"录入员工信息",
            visible:false,
            employees:[],
            form:{
                type:"waiter"
            }
        }
    },
    created(){
        //在页面加载出来之前加载数据
        this.loadData();
    },
}
</script>

<style scoped>
       
</style>