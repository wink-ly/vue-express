<template>
    <div class="body">
        <section class="container">
            <h1>请登录</h1>
            <el-form class="textarea" ref="loginForm" :model="loginUser" :rules="rules">
                <el-form-item prop="name">
                    <el-input type="text" class="inputDeep" v-model="loginUser.name" placeholder="请输入用户名"
                        autocomplete="off" />
                </el-form-item>
                <el-form-item prop="password">
                    <el-input type="password" v-model="loginUser.password" placeholder="请输入密码" autocomplete="off" 
                    @keyup.enter.native="submitForm(loginForm)"/>
                </el-form-item>
                <el-button @click="submitForm(loginForm)" >登录</el-button>
                <p class="text">没有账户? <router-link to="/register" replace>注册一个吧</router-link> </p>
            </el-form>
        </section>
    </div>
</template>

<script setup>
import { reactive, ref, getCurrentInstance } from "vue";
import { useRouter } from "vue-router";
import { useStore } from "vuex";
import jwt_decode from "jwt-decode";
const router = useRouter();
const store = useStore();
const { proxy } = getCurrentInstance();

const loginForm = ref(null);

const loginUser = reactive({
    name: "",
    password: "",
});

const rules = reactive({
    name: [
        {
            required: true,
            message: "用户名不能为空",
            trigger: "blur",
        },
    ],
    password: [
        {
            required: true,
            message: "密码不能为空",
            trigger: "blur",
        },
        {
            min: 6,
            max: 30,
            message: "长度在6到30个字符",
            trigger: "blur",
        },
    ],
});

// 登录事件
const submitForm = (loginForm) => {
    loginForm.validate((valid) => {
        if (valid) {
            proxy.$axios
                .post("/admin/login", loginUser)
                .then((res) => {
                    // 登录成功 拿到token 
                    proxy.$message({
                        type: "success",
                        message: "登录成功",
                    });
                    const { token } = res.data;
                    // 存储到localStorage
                    localStorage.setItem("mytoken", token);
                    // 解析token
                    const decoded = jwt_decode(token);
                    // token存储到vuex中
                    store.dispatch("setAuthenticated", !isEmpty(decoded));
                    store.dispatch("setUser", decoded);
                    // 路由跳转
                    router.push("/index");
                })
        } else {
            proxy.$message({
                type: "error",
                message: "错误提交申请",
            });
            return false;
        }
    });
};

// 判断是否为空的方法
const isEmpty = (value) => {
    return (
        value === undefined ||
        value === null ||
        (typeof value === "object" && Object.keys(value).length === 0) ||
        (typeof value === "string" && value.trim().length === 0)
    );
};
</script>

<style lang="less" scoped>
.body {
    background-image: url(../../assets/images/top3.png);
    background-size: cover;
    color: #fff;
    height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
}

.container {
    padding: 20px 40px;
    border-radius: 5px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 5);
}

h1 {
    text-align: center;
    margin-bottom: 30px;
}

.textarea :deep(.el-form-item) {
    position: relative;
    width: 300px;
    margin: 20px 0 40px;
}

.textarea :deep(.el-input__inner) {
    color: #fff;
    font-size: 18px;
}


.textarea :deep(.el-input__wrapper) {
    background-color: transparent;
    box-shadow: none;
    display: block;
    border-radius: 0;
    border-bottom: 2px solid #fff;
}

.textarea :deep(.el-input__wrapper:hover,
    .el-form-item.is-error .el-input__wrapper) {
    box-shadow: none;
}

.textarea :deep(.el-input__wrapper.is-focus) {
    box-shadow: none;
    border-bottom-color: lightblue;
}

.textarea :deep(.el-button) {
    background: lightblue;
    color: #000000;
    height: 45px;
    width: 100%;
    padding: 14px;
    font-size: 16px;
    border: 0;
    border-radius: 5px;
}

.textarea :deep(.el-button:active) {
    transform: scale(0.98);
}

.text {
    margin-top: 15px;
}

a {
    text-decoration: none;
    color: lightblue;
}
</style>