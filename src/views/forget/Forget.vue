<template>
    <div class="body">
        <div class="container">
            <h2>修改密码</h2>
            <el-form ref="editForm" :model="editUser" status-icon :rules="rules">
                <el-form-item prop="name">
                    <label>用户名</label>
                    <el-input v-model="editUser.name" type="text" autocomplete="off" placeholder="请输入用户名" />
                </el-form-item>
                <el-form-item prop="password">
                    <label>新密码</label>
                    <el-input v-model="editUser.password" type="password" autocomplete="off" placeholder="请输入密码"
                        show-password />
                </el-form-item>
                <el-form-item prop="confirmPassword">
                    <label>确认新密码</label>
                    <el-input v-model="editUser.confirmPassword" type="password" autocomplete="off" placeholder="请再次输入密码"
                        show-password />
                </el-form-item>
                <div class="bt">
                    <el-button class="btn" type="primary" @click="goback()">返回</el-button>
                    <el-button class="btn" type="primary" @click="submitForm(editForm)">修改</el-button>
                </div>
            </el-form>
        </div>
    </div>
</template>
    
<script setup>
import { reactive, ref, getCurrentInstance } from "vue";
import { useRouter } from "vue-router";
const router = useRouter();
const { proxy } = getCurrentInstance();

const editForm = ref(null);

const editUser = reactive({
    name: "",
    password: "",
    avatar: "https://www.lingnotes.fun/touxiang.png",
    confirmPassword: "",
});

const validatePass2 = (rule, value, callback) => {
    if (value !== editUser.password) {
        callback(new Error("两次输入密码不一致!"));
    } else {
        callback();
    }
};

const rules = reactive({
    name: [
        {
            required: true,
            message: "用户名不能为空",
            trigger: "blur",
        },
        {
            min: 2,
            max: 30,
            message: "长度要在2-30个字符之间",
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
    confirmPassword: [
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
        {
            validator: validatePass2,
            trigger: "blur",
        },
    ],
});

const submitForm = (editForm) => {
    editForm.validate((valid) => {
        if (valid) {
            proxy.$axios
                .post("/admin/editpsd", editUser)
                .then(() => {
                    proxy.$message({
                        type: "success",
                        message: "修改成功",
                    });
                    // 路由跳转
                    router.push("/login");
                })
        }
    });
};
// 返回
const goback = () => {
    router.push("/login");
}
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
    overflow: hidden;
    margin: 0 !important;
}

.container {
    box-shadow: 0 0 10px rgba(0, 0, 0, 5);
    padding: 20px 40px;
    border-radius: 5px;
}

h2 {
    text-align: center;
}

.el-form-item {
    position: relative;
    width: 100%;
}

.el-form-item label {
    width: 30%;
    text-align: center;
}

.el-form-item .el-input {
    width: 65%;
}

.bt {
    display: flex;
    align-items: center;
    justify-content: center;
}

.btn {
    background-color: lightblue;
    color: #000000;
    width: 40%;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 15px;
    font-size: 16px;
    margin-bottom: 15px;
    border: 0;
    border-radius: 5px;
}

.btn:active {
    transform: scale(0.98);
}
</style>