<template>
    <div>
        <!-- 按钮 -->
        <el-button type="warning" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small" @click="toDeleteHandler">删除</el-button>
        <!-- 表格 -->
        <el-table :data="employees">
            <el-table-column label="编号" prop="id"></el-table-column>
            <el-table-column label="用户名" prop="username"></el-table-column>
            <el-table-column label="真实姓名" prop="realname"></el-table-column>
            <el-table-column label="性别" prop="gender"></el-table-column>
            <el-table-column label="联系方式" prop="telephone"></el-table-column>
            <el-table-column label="身份证号" prop="idCard"></el-table-column>
            <el-table-column label="银行卡号" prop="bankCard"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <!-- {{slot.row}} -->
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)" >删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!-- 分页 -->
        <el-pagination layout="prev, pager, next" :total="50">
        </el-pagination>
        <!-- 模态框 -->
        <el-dialog :title="title"  :visible.sync="visible"  width="60%">
            {{form}}
            <el-form label-width="80px" :model="form">
                <el-form-item label="用户名">
                    <el-input v-model="form.username"></el-input>
                </el-form-item>
                <el-form-item label="密码">
                    <el-input type="password" v-model="form.password"></el-input>
                </el-form-item>
                <el-form-item label="真实姓名">
                    <el-input v-model="form.realname"></el-input>
                </el-form-item>
                <el-form-item label="性别">
                    <el-radio-group v-model="form.gender">
                        <el-radio v-model="radio" label="男">男</el-radio>
                        <el-radio v-model="radio" label="女">女</el-radio>
                    </el-radio-group>
                </el-form-item>
                <el-form-item label="身份证号">
                    <el-input v-model="form.idCard"></el-input>
                </el-form-item>
                <el-form-item label="手机号">
                    <el-input v-model="form.telephone"></el-input>
                </el-form-item>
                <el-form-item label="银行卡号">
                    <el-input v-model="form.bankCard"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
            <el-button @click="visible = false" size="small" type="primary">取 消</el-button>
            <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    data(){
        return{
            title:"录入员工信息",
            visible:false,
            employees:[],
            form:{
                type:"waiter"
            }
        }
    },
    created(){
        this.loadData();
    },
    methods:{
        loadData(){
            let url="http://localhost:6677/waiter/findAll"
            request.get(url).then((response)=>{//箭头函数中的this指向外部函数中的this
                this.employees=response.data
            }) 
        },
        submitHandler(){
            let url="http://localhost:6677/waiter/saveOrUpdate"
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                this.closeModalHandler();
                this.loadData();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })

        },
        toAddHandler(id){
            this.title="添加员工信息";
            this.visible=true;
        },
        toDeleteHandler(id){
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
        }

    }
}
</script>

<style scoped>

</style>