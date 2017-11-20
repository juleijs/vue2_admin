<template>
    <el-row class="warp">
        <el-col :span="24" class="warp-breadcrumb">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
                <el-breadcrumb-item>图书管理</el-breadcrumb-item>
                <el-breadcrumb-item>图书列表</el-breadcrumb-item>
            </el-breadcrumb>
        </el-col>

        <el-col :span="24" v-loading="loading" class="warp-main" element-loading-text="拼命加载中">
            <!-- 工具条 -->
            <el-col :span="24" class="toolbar">
                <el-form :inline="true" :module="filters" onsubmit="return false">
                    <el-form-item>
                        <el-input v-model="filters.name" placeholder="书名" @keyup.enter.native="handleSearch"></el-input>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="handleSearch">查询</el-button>
                    </el-form-item>
                    <el-form-item>
                        <el-button type="primary" @click="showAddDialog">新增</el-button>
                    </el-form-item>
                </el-form>
            </el-col>
            <!-- 列表 -->
            <el-table :data="books" highlight-current-row @selection-change="selsChange">
                <el-table-column type="selection" width="55"></el-table-column>
                <el-table-column type="index" width="60"></el-table-column>
                <el-table-column type="expand">
                    <template scope="props">
                        <el-form label-position="left" calss="demo-table-expand" inline>
                            <el-form-item label="[图书简介]">
                                <span>{{props.row.description}}</span>
                            </el-form-item>
                        </el-form>
                    </template>
                </el-table-column>
                <el-table-column prop="name" label="书名" sortable></el-table-column>
                <el-table-column prop="author" label="作者" width="100" sortable></el-table-column>
                <el-table-column prop="publishAt" label="出版日期" width="150" sortable></el-table-column>
                <el-table-column label="操作" width="150">
                    <template scope="scope">
                        <el-button size="small" @click="showEditDialog(scope.$index,scope.row)">编辑</el-button>
                        <el-button size="small" type="danger" @click="delBook(scope.$index,scope.row)">删除</el-button>
                    </template>
                </el-table-column>
            </el-table>
            <!-- 工具条 -->
            <el-col :span="24" class="toolbar">
                <el-button type="danger" size="small" @click="batchDeleteBook" :disabled="this.sels.length === 0">批量删除</el-button>
                <el-pagination layout="prev,pager,next" @current-change="handleCurrentChange" :page-siz="10" :total="total"></el-pagination>
            </el-col>
            <!-- 编辑页面 -->
            <el-dialog title="编辑" :visible.sync="editFormVisible" :close-on-click-modal="false">
                <el-form :model="editForm" label-width="100px" :rules="FormRules" ref="editForm">
                    <el-form-item label="书名" prop="name">
                        <el-input v-model="editForm.name" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="作者" prop="author">
                        <el-input v-model="editForm.author" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="出版日期">
                        <el-date-picker type="date" placeholder="选择日期" v-model="editForm.publishAt"></el-date-picker>
                    </el-form-item>
                    <el-form-item label="简介" prop="description">
                        <el-input type="textarea" v-model="editForm.description" :rows="8"></el-input>
                    </el-form-item>
                </el-form>
                <div class="dialog-footer" slot="footer">
                    <el-button @click.native="editFormVisible=false">取消</el-button>
                    <el-button type="primary" @click.native="editSubmit">提交</el-button>
                </div>
            </el-dialog>
            <!-- 新增页面 -->
            <el-dialog title="新增" :visible.sync="addFormVisible" :close-on-click-model="false">
                <el-form :model="addForm" label-width="100px" :rules="FormRules" ref="addForm">
                    <el-form-item label="书名" prop="name">
                        <el-input v-model="addForm.name" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="作者" prop="author">
                        <el-input v-model="addForm.author" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="出版日期">
                        <el-date-picker type="date" placeholder="选择日期" v-model="addForm.publishAt"></el-date-picker>
                    </el-form-item>
                    <el-form-item label="简介" prop="description">
                        <el-input type="textarea" v-model="addForm.description" :rows="8"></el-input>
                    </el-form-item>
                </el-form>
                <div class="dialog-footer" slot="footer">
                    <el-button @click.native="addFormVisible=false">取消</el-button>
                    <el-button type="primary" @click.native="addSubmit" :loading="addLoading">提交</el-button>
                </div>
            </el-dialog>
        </el-col>
    </el-row>
</template>

<script>

import util from '../../common/util'
import API from '../../api/api_book'
import moment from 'moment'
export default {
    data() {
        return {
            loading: false,
            filters: {
                name: ''
            },
            books: [],
            limit: 10,
            page: 1,
            total: 0,
            sels: [],//列表选中列
            editFormVisible: false,
            addFormVisible: false,
            //编辑相关数据
            editForm: {
                id: 0,
                name: '',
                author: '',
                publishAt: '',
                description: ''
            },
            //新增相关数据
            addForm: {
                name: '',
                author: '',
                publishAt: '',
                description: ''
            },
            //编辑、新增表单验证规则
            FormRules: {
                name: [
                    { required: true, message: '请输入书名', trigger: 'blur' }
                ],
                author: [
                    { required: true, message: '请输入作者', trigger: 'blur' }
                ],
                description: [
                    { required: true, message: '请输入简介', trigger: 'blur' }
                ]
            },
            addLoading: false
        }
    },
    methods: {
        search() {
            let _this = this;
            let params = {
                page: _this.page,
                limit: 10,
                name: _this.filters.name
            };
            _this.loading = true;
            API.findList(params).then(function(result) {
                _this.loading = false;
                if (result && result.books) {
                    _this.total = result.total;
                    _this.books = result.books;
                }
            }, function(err) {
                _this.loading = false;
                _this.$message.error({ showClose: true, message: err.toString(), duration: 2000 });
            }).catch(function(error) {
                _this.loading = false;
                console.log(error);
                _this.$message.error({ showClose: true, message: '请求出现异常', duration: 2000 });
            })
        },
        handleSearch() {
            this.total = 0;
            this.page = 1;
            this.search();
        },
        showAddDialog() {
            this.addFormVisible = true;
        },
        showEditDialog(index, row) {
            this.editFormVisible = true;
            this.editForm = Object.assign({}, row);
        },
        //删除
        delBook(index, row) {
            let _this = this;
            this.$confirm('确定删除该记录吗?', '提示', { type: 'warning' }).then(() => {
                _this.loading = true;
                API.remove(row.id).then(function(result) {
                    _this.loading = false;
                    if (result && parseInt(result.errcode) === 0) {
                        _this.$message.success({ showClose: true, message: '删除成功', duration: 1500 });
                        _this.search();
                    }
                }, function(err) {
                    _this.loading = false;
                    _this.$message.error({ showClose: true, message: err.toString(), duration: 2000 });
                }).catch(function(error) {
                    _this.loading = false;
                    console.log(error);
                    _this.$message.error({ showClose: true, message: '请求出现异常', duration: 2000 });
                })
            }).catch(() => { })
        },
        selsChange(sels) {
            this.sels = sels;
        },
        handleCurrentChange(val) {
            this.page = val;
            this.search();
        },
        batchDeleteBook() {
            let _this = this;
            let ids = _this.sels.map(item => item.id).toString();
            _this.$confirm('确定批量删除吗?', '提示', { type: 'warning' }).then(() => {
                _this.loading = true;
                API.removeBatch(ids).then(function(result) {
                    _this.loading = false;
                    if (result && parseInt(result.errcode) === 0) {
                        _this.$message.success({ showClose: true, message: '批量删除成功', duration: 1500 });
                        _this.search();
                    } else {
                        _this.$message.error({ showClose: true, message: '批量删除错误', duration: 2000 });
                    }
                }, function(err) {
                    _this.loading = false;
                    _this.$message.error({ showClose: true, message: err.toString(), duration: 2000 });
                }).catch(function(error) {
                    _this.loading = false;
                    console.log(error);
                    _this.$message.error({ showClose: true, message: '请求出现异常', duration: 2000 });
                })
            }).catch(() => { })

        },
        //编辑
        editSubmit() {
            let _this = this;
            this.$refs.editForm.validate((value) => {
                if (value) {
                    _this.loading = true;
                    let para = Object.assign({}, _this.editForm);
                    // para.publishAt = (!para.publishAt || para.publishAt == '') ? '' : util.format(new Date(para.publishAt), 'yyyy-MM-dd');
                    para.publishAt = (!para.publishAt || para.publishAt == '') ? '' : moment(new Date(para.publishAt)).format('YYYY-MM-DD');
                    API.update(para.id, para).then(function(result) {
                        _this.loading = false;
                        if (result && parseInt(result.errcode) === 0) {
                            _this.$message.success({ showClose: true, message: '修改成功', duration: 2000 });
                            // _this.$refs['editForm'].resetFields();
                            _this.editFormVisible = false;
                            _this.search();
                        } else {
                            _this.$message.error({ showClose: true, message: '修改失败', duration: 2000 });
                        }
                    }, function(err) {
                        _this.loading = false;
                        _this.$message.error({ showClose: true, message: err.toString(), duration: 2000 });
                    }).catch(function(error) {
                        _this.loading = false;
                        console.log(error);
                        _this.$message.error({ showClose: true, message: '请求出现异常', duration: 2000 });
                    })
                }
            })
        },
        //新增
        addSubmit() {
            let _this = this;
            _this.$refs.addForm.validate((value) => {
                if (value) {
                    _this.loading = true;
                    let para = Object.assign({}, _this.addForm);
                    para.publishAt = (!para.publishAt || para.publishAt == '') ? '' : moment(new Date(para.publishAt)).format('YYYY-MM-DD');
                    API.add(para).then(function(result) {
                        _this.loading = false;
                        if (result && parseInt(result.errcode) === 0) {
                            _this.$message.success({ showClose: true, message: '新增成功', duration: 2000 });
                            _this.addFormVisible = false;
                            _this.search();
                        } else {
                            _this.$message.error({ showClose: true, message: '新增失败', duration: 2000 });
                        }

                    }, function(err) {
                        _this.loading = false;
                        _this.$message.error({ showClose: true, message: err.toString(), duration: 2000 });
                    }).catch(function(error) {
                        _this.loading = false;
                        console.log(error);
                        _this.$message.error({ showClose: true, message: '请求出现异常', duration: 2000 });
                    })
                }
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

.el-pagination {
    float: right;
}

.toolbar {
    margin-top: 10px;
}
</style>

