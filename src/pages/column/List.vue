<template>
    <div>
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small" @click="toDeleteHandler">批量删除</el-button>
        <el-table :data="columns">
            <el-table-column label="编号" prop="id"></el-table-column>
            <el-table-column label="栏目名称" prop="name"></el-table-column>
            <el-table-column label="序号" prop="num"></el-table-column>
            <el-table-column label="父栏目" prop="parentId"></el-table-column>
            <el-table-column label="操作">
                <template>
                    <el-button type="primary" icon="el-icon-delete" @click="toDeleteHandler(slot.row.id)"></el-button>
                    <el-button type="primary" icon="el-icon-edit" @click="toUpdateHandler(slot.row)"></el-button>
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
                <el-form-item label="编号">
                    <el-input v-model="form.id"></el-input>
                </el-form-item>
                <el-form-item label="项目名称">
                    <el-input  v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="序号">
                    <el-input v-model="form.num"></el-input>
                </el-form-item>
                <el-form-item label="父栏目">
                    <el-input v-model="form.parentId"></el-input>
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
            title:"添加栏目信息",
            visible:false,
            columns:[],
            form:{
                type:"column"
            }
        }
    },
    created(){
        this.loadData();
    },
    methods:{
        loadData(){
            let url="http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{//箭头函数中的this指向外部函数中的this
                this.columns=response.data
            }) 
        },
        submitHandler(){
            let url="http://localhost:6677/category/saveOrUpdate"
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
            this.title="添加栏目信息";
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
            this.title="修改栏目信息";
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