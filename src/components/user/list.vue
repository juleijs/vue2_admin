<template>
    <el-row class="warp">
        <el-col :span="24" class="warp-breadcrumb">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item :to="{ path: '/' }">
                    首页
                </el-breadcrumb-item>
                <el-breadcrumb-item>用户列表</el-breadcrumb-item>
            </el-breadcrumb>
        </el-col>
        <el-col :span="24" class="warp-main" v-loading="loading" element-loading-text="拼命加载中">
            <!-- 工具条 -->
            <el-col :span="24" class="toolbar">
                <el-form :inline="true" :model="filters" onsubmit="return false">
                    <el-form-item>
                        <el-input v-model="filters.name" placeholder="用户名/姓名/昵称" @keyup.enter.native="handleSearch"></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="handleSearch">查询</el-button>
                    </el-form-item>
                </el-form>
            </el-col>

            <!-- 列表 -->
            <el-table :data="users" highlight-current-row v-loading="loading" :default-sort="{order: 'descending'}">
                <el-table-column type="index" width="60"></el-table-column>
                <el-table-column prop="name" label="姓名" width="120" sortable></el-table-column>
                <el-table-column prop="nickname" label="昵称" width="120" sortable></el-table-column>
                <el-table-column prop="sex" label="性别" :formatter="formatSex" width="100" sortable></el-table-column>
                <el-table-column prop="email" label="邮箱" width="500" sortable></el-table-column>
                <el-table-column prop="addr" label="地址" sortable></el-table-column>
            </el-table>
        </el-col>
    </el-row>
</template>

<script>

import API from '../../api/api_user'
export default {
    data() {
        return {
            loading: false,
            filters: {
                name: ''
            },
            users: [
            ],
            total: 0,
            page: 1
        }
    },
    methods: {
        formatSex(row, column) {
            return row.sex == 1 ? '男' : row.sex == 0 ? '女' : '未知';
        },
        handleSearch() {
            this.total = 0;
            this.page = 1;
            this.search();
        },
        //获取用户列表
        search() {
            let _this = this;
            let params = {
                page: _this.page,
                limit: 10,
                name: _this.filters.name
            }

            _this.loading = true;
            API.findList(params).then(function(result) {
                _this.loading = false;
                if (result && result.users) {
                    _this.total = result.total;
                    _this.users = result.users;
                }
            }, function(err) {
                _this.loading = false;
                _this.$message.error({ showClose: true, message: err.toString(), duration: 2000 });
            }).catch(function(error) {
                _this.loading = false;
                console.log(error);
                _this.$message.error({ showClose: true, message: '请求出现异常', duration: 2000 })
            })
        }
    },
    mounted() {
        this.handleSearch();
    }
}
</script>

<style scoped>
.warp-breadcrumb {
    margin: 5px 10px 15px 0;
}

.el-table {
    width: 100%;
}
</style>


