<template>
    <el-form ref="AccountFrom" :model="account" :rules="rules" label-position="left" label-width="0px" class="demo-ruleForm login-container">
        <h3 class="title">管理员登录</h3>
        <el-form-item prop="username">
            <el-input type="text" v-model="account.username" auto-complete="off" placeholder="账号"></el-input>
        </el-form-item>
        <el-form-item prop="pwd">
            <el-input type="password" v-model="account.pwd" auto-complete="off" placeholder="密码"></el-input>
        </el-form-item>
        <el-checkbox v-model="checked" checked class="remember">记住密码</el-checkbox>
        <el-form-item styel="width:100%;">
            <el-button type="primary" style="width:100%;" @click.native.prevent="handleLogin" :loading="loading">登录</el-button>
        </el-form-item>
    </el-form>
</template>

<script>
import API from '../api/api_user'
export default {
    data() {
        return {
            loading: false,
            account: {
                username: 'admin',
                pwd: '123456'
            },
            rules: {
                username: [
                    { required: true, message: '请输入账号', trigger: 'blur' },
                    // { validator: validaePass }
                ],
                pwd: [
                    { required: true, message: '请输入密码', trigger: 'blur' },
                ]
            },
            checked: true
        }
    },
    methods: {
        handleLogin() {
            let _this = this;
            this.$refs.AccountFrom.validate((valid) => {
                if (valid) {
                    this.loading = true;
                    let loginParams = { username: this.account.username, pwd: this.account.pwd };
                    API.login(loginParams).then(function(result) {
                        _this.loading = false;
                        if (result && result.id) {
                            localStorage.setItem('access-user', JSON.stringify(result));
                            _this.$router.push({ path: '/' });
                        } else {
                            _this.$message.error({ showClose: true, message: result.errmsg || '登录失败', duration: 2000 });
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
    }
}
</script>

<style>
body {
    background: #dfe9fb;
}
</style>

<style lang="scss" scoped>
.login-container {
    width: 350px;
    -webkit-border-radius: 5px;
    border-radius: 5px;
    -moz-border-radius: 5px;
    background-clip: padding-box;
    margin: 160px auto;
    padding: 35px 35px 15px 35px;
    border: 1px solid #ff0;
    box-shadow: 0 0 25px #cac6c6;

    background: -webkit-linear-gradient(top, #acc, #ace);
}

.title {
    margin: 0 auto 40px auto;
    color: #505458;
    text-align: center;
}

.remember {
    margin: 0px 0px 25px 0px;
}
</style>
