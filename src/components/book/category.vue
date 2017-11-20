<template>
    <el-row class="warp">
        <el-col :span="24" class="warp-breadcrumb">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
                <el-breadcrumb-item>图书管理</el-breadcrumb-item>
                <el-breadcrumb-item>图书分类</el-breadcrumb-item>
            </el-breadcrumb>
        </el-col>
        <el-col class="warp-main">
            <h2>{{message}}</h2>
        </el-col>
    </el-row>
</template>

<script>
export default {
    data() {
        return {
            message: '哥,看啥呢?啥都没哟哦！'
        }
    }
}
</script>


<style scoped>
.warp-breadcrumb {
    margin: 5px 10px 15px 0;
}
</style>

