<template>
    <div>
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="primary" size="small" @click="toDeleteHandler">批量删除</el-button>
        <el-table :data="products">
            <el-table-column label="编号" prop="id"></el-table-column>
            <el-table-column label="产品名称" prop="name"></el-table-column>
            <el-table-column label="价格" prop="price"></el-table-column>
            <el-table-column label="描述" prop="description"></el-table-column>
            <el-table-column label="所属产品" prop="status"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                <el-link icon="el-icon-edit" @click.passive="toAddHandler(slot.row)">添加</el-link>
                <el-link icon="el-icon-delete" @click.prevent="toDeleteHandler(slot.row.id)">删除</el-link>
                </template>
            </el-table-column>
        </el-table>
        <!-- 分页 -->
        <!-- <el-pagination layout="prev, pager, next" :total="50"> -->
        <!-- </el-pagination> -->
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
    methods:{
        loadData(){
            let url="http://localhost:6677/product/findAll"
            request.get(url).then((response)=>{
                this.products=response.data
            })
        },
        submitHandler(){
            let url="http://localhost:6677/product/saveOrUpdate"
            request({
                url,
                method:"post",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                }
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
            this.title="添加产品信息";
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
            this.title="修改产品信息";
            this.visible=true;

        },
        closeModalHandler(){
            this.visible=false;
        }

    },
    created(){
        this.loadData();
    },
    data(){
        return{
            title:"录入产品信息",
            visible:false,
            products:[],
            form:{
                type:"product"
            }
        }

    }
}
</script>
<style scoped>

</style>