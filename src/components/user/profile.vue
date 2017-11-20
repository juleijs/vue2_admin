<template>
    <el-row class="warp">
        <el-col :span="24" class="warp-breadcrumb" :loading="loading">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item :to="{ path: '/' }">
                    首页
                </el-breadcrumb-item>
                <el-breadcrumb-item>设置</el-breadcrumb-item>
                <el-breadcrumb-item>个人信息</el-breadcrumb-item>
            </el-breadcrumb>
        </el-col>
        <el-col :span="24" class="warp-main">
            <el-form ref="form" :model="form" :rules="rules" label-width="80px">
                <el-form-item label="账号">
                    <el-input v-model="form.username" disabled></el-input>
                </el-form-item>
                <el-form-item label="昵称" prop="nickname">
                    <el-input v-model="form.nickname"></el-input>
                </el-form-item>
                <el-form-item label="姓名" prop="name">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="邮箱" prop="email">
                    <el-input v-model="form.email"></el-input>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" @click="handleSaveProfile">修改并保存</el-button>
                </el-form-item>
            </el-form>
        </el-col>
    </el-row>
</template>

<script>

import API from '../../api/api_user'
import { bus } from '../../bus.js'
export default {
    data() {
        return {
            loading: false,
            form: {
                username: '',
                nickname: '',
                name: '',
                email: ''
            },
            rules: {
                nickname: [
                    {
                        required: true,
                        message: '请输入昵称',
                        trigger: 'blur'
                    }
                ],
                email: [
                    {
                        required: true,
                        message: '请输入邮箱',
                        trigger: 'blur'
                    },
                    {
                        type: 'email',
                        message: '请输入正确的邮箱地址',
                        trigger: 'blur,change'
                    }
                ]
            }
        }
    },
    methods: {
        handleSaveProfile() {
            let _this = this;
            _this.$refs.form.validate((valid) => {
                if (valid) {
                    _this.loading = true;
                    let args = {
                        nickname: _this.form.nickname,
                        name: _this.form.name,
                        email: _this.form.email
                    }

                    API.changeProfile(args).then(function(result) {
                        _this.loading = false;
                        if (result && parseInt(result.errcode) === 0) {
                            let user = JSON.parse(window.localStorage.getItem('access-user'))
                            user.nickname = _this.form.nickname;
                            user.name = _this.form.name;
                            user.email = _this.form.email;
                            localStorage.setItem('access-user', JSON.stringify(user));
                            bus.$emit('setNickName', _this.form.nickname);
                            _this.$message.success({ showClose: true, message: '修改成功', duration: 2000 })
                        } else {
                            _this.$message.error({ showClose: true, message: result.errmsg, duration: 2000 })
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
        let user = localStorage.getItem('access-user');
        if (user) {
            user = JSON.parse(user);
            this.form.username = user.username;
            this.form.nickname = user.nickname || '';
            this.form.name = user.name || '';
            this.form.email = user.email || '';
        }
    }
}
</script>

<style scoped>
.warp-breadcrumb {
    margin: 5px 10px 15px 0;
}
</style>





