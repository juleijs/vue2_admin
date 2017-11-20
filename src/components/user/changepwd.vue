<template>
    <el-row class="warp">
        <el-col :span="24" class="warp-breadcrumb" :loading="loading">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item :to="{ path: '/' }">
                    首页
                </el-breadcrumb-item>
                <el-breadcrumb-item>设置</el-breadcrumb-item>
                <el-breadcrumb-item>修改密码</el-breadcrumb-item>
            </el-breadcrumb>
        </el-col>
        <el-col :span="24" class="warp-main">
            <el-form ref="form" :model="form" :rules="rules" label-width="100px">
                <el-form-item prop="oldpwds" label="原密码">
                    <el-input v-model="form.oldPwd"></el-input>
                </el-form-item>
                <el-form-item label="新密码" prop="newPwds">
                    <el-input v-model="form.newPwd"></el-input>
                </el-form-item>
                <el-form-item label="确认新密码" prop="confirmPwds">
                    <el-input v-model="form.confirmPwd"></el-input>
                </el-form-item>
                <el-form-item>
                    <el-button @click="handleChangepwd">提交</el-button>
                </el-form-item>
            </el-form>
        </el-col>
    </el-row>
</template>

<script>
export default {
    data() {
        return {
            loading: false,
            rules: {
                oldpwds: [
                    {
                        required: true,
                        message: '请输入原密码',
                        trigger: 'blur'
                    },
                ],
                newPwds: [
                    {
                        required: true,
                        message: '请输入新密码',
                        trigger: 'blur'
                    }
                ],
                confirmPwds: [
                    {
                        required: true,
                        message: '请输入确认新密码',
                        trigger: 'blur'
                    }
                ]
            },
            form: {
                oldPwd: '',
                newPwd: '',
                confirmPwd: ''
            }
        }
    },
    methods: {
        handleChangepwd() {
            this.$message({ message: '这个功能只是看看的咯,还没有开发嘞！', duration: 2000 })
        }
    }
}
</script>

<style scoped>
.warp-breadcrumb {
    margin: 5px 10px 15px 0;
}
</style>
