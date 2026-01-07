<template>
  <Toast position="bottom-center" />
  <div class="navbar">
    <div class="navbar-container">
      <div class="brand">
        <div class="logo">
          五尊佛飯店 HOTEL
          <small>WUZUNFO GRAND HOTEL</small>
        </div>
      </div>

      <div class="nav-links">
        <router-link to="/HomePage">首頁</router-link>
        <router-link to="/Roomfacility">飯店設施</router-link>
        <router-link to="/Booking">線上訂房</router-link>
        <router-link to="/OrderInquiry">訂單查詢</router-link>
        <router-link to="/RoomIntro">客房介紹</router-link>
        <router-link to="/Conteact">聯絡我們</router-link>
        <router-link to="/MemberCenter" v-if="member">會員中心</router-link>
      </div>

      <div class="nav-cta">
        <button class="btn" onclick="w3_open()">☰ 選單</button>
        <Button
          label="登入"
          severity="contrast"
          @click="meberVisible = true"
        ></Button>
      </div>
    </div>
  </div>

  <!-- 會員資訊的彈跳視窗 -->
  <Dialog
    v-model:visible="meberVisible"
    header="會員登入"
    :style="{ width: '25rem' }"
    appendTo="body"
  >
    <span style="display: flex; justify-content: center; margin-bottom: 15px"
      >請輸入您的帳號密碼:</span
    >
    <div class="LogOrPsw">
      <div>
        <label for="email">信箱:</label>
        <InputText id="email" v-model="email" autocomplete="off" />
      </div>

      <div>
        <label for="passowrd">密碼:</label>
        <InputText
          id="passowrd"
          v-model="password"
          autocomplete="off"
          style="margin-top: 15px"
        />
      </div>
    </div>

    <div class="LogOrRes">
      <Button type="button" label="登入" @click="Login"></Button>
      <Button
        style="margin-left: 12px"
        type="button"
        label="註冊"
        severity="secondary"
        @click="
          meberVisible = false;
          Resigter = true;
        "
      ></Button>
    </div>
  </Dialog>

  <!-- 註冊的彈跳視窗 -->
  <Dialog
    v-model:visible="Resigter"
    header="會員註冊"
    :style="{ width: '30rem' }"
    appendTo="body"
  >
    <div class="register-form">
      <div class="form-field">
        <label for="register-name">姓名</label>
        <InputText v-model="name" placeholder="請輸入真實姓名" />
      </div>

      <!-- <div class="form-field">
        <label for="register-username">帳號</label>
        <InputText v-model="userName" placeholder="請設定登入帳號" />
      </div> -->

      <div class="form-field">
        <label for="register-email">電子信箱</label>
        <InputText
          v-model="email"
          type="email"
          placeholder="example@email.com"
        />
      </div>

      <div class="form-field">
        <label for="register-password">密碼</label>
        <InputText
          v-model="password"
          type="password"
          placeholder="請設定密碼"
        />
      </div>
    </div>

    <div class="register-footer">
      <Button
        type="button"
        label="返回登入"
        icon="pi pi-arrow-left"
        severity="secondary"
        text
        @click="
          Resigter = false;
          meberVisible = true;
        "
      >
      </Button>
      <Button
        type="button"
        label="確認註冊"
        icon="pi pi-check"
        @click="register"
      >
      </Button>
    </div>
  </Dialog>
</template>

<script setup>
import { ref } from "vue";
import Button from "primevue/button";
import Dialog from "primevue/dialog";
import InputText from "primevue/inputtext";
import Toast from "primevue/toast";
import { useToast } from "primevue/usetoast";

import api from "../api/api";
//
const toast = useToast();

const meberVisible = ref(false);
const Resigter = ref(false);
//會員註冊
const password = ref("");
const email = ref("");
const name = ref("");
//會員介面
const member = ref(false);
async function register() {
  if (email.value === "" || password.value === "" || name.value === "") {
    toast.add({
      severity: "error",
      summary: "錯誤",
      detail: "欄位請勿留白",
      life: 3000,
    });
  } else {
    try {
      const registers = {
        FullName: name.value,
        Email: email.value,
        Password: password.value,
      };

      await api.post("UsersRegister/register", registers);

      Resigter.value = false;

      toast.add({
        severity: "success",
        summary: "註冊成功",
        detail: "歡迎加入會員！",
        life: 3000,
      });
    } catch (error) {
      console.error(error);

      // 判斷是否為後端回傳的錯誤訊息
      const errMsg = error.response?.data || "註冊失敗，請稍後再試";

      toast.add({
        severity: "error",
        summary: "註冊失敗",
        detail: errMsg, // 顯示後端回傳的具體錯誤
        life: 3000,
      });
    }
  }
}

//會員登入
async function Login() {
  if (email.value === "" || password.value === "") {
    toast.add({
      severity: "error",
      summary: "錯誤",
      detail: "信箱或密碼錯誤",
      life: 3000,
    });
  } else {
    try {
      const logins = {
        Email: email.value,
        Password: password.value,
      };

      await api.post("Users/login", logins);

      meberVisible.value = false;
      member.value = true;
      toast.add({
        severity: "success",
        summary: "登入成功",
        detail: "歡迎回來",
        life: 3000,
      });
    } catch (error) {
      const errMsg = error.response?.data || "請確認帳號密碼是否正確";

      toast.add({
        severity: "error",
        summary: "登入失敗",
        detail: "帳號或密碼錯誤",
        life: 3000,
      });
    }
  }
}
</script>

<style>
.register-form {
  display: flex;
  flex-direction: column; /* 垂直排列 */
  gap: 1.5rem; /* 每個欄位之間的間距 */
  padding: 10px 0;
}

/* 每個欄位的包裝 */
.form-field {
  display: flex;
  flex-direction: column; /* 讓 Label 在 Input 上方 */
  gap: 0.5rem; /* Label 和 Input 之間的距離 */
}

/* 讓 Label 文字稍微大一點，加粗 */
.form-field label {
  font-weight: bold;
  font-size: 0.95rem;
  color: #555;
}

/* 讓 Input 寬度填滿 */
.form-field .p-inputtext {
  width: 100%;
}

/* 按鈕區域排版 */
.register-footer {
  display: flex;
  justify-content: space-between; /* 一左一右分開 */
  margin-top: 2rem;
  padding-top: 1rem;
  border-top: 1px solid #eee; /* 上方加一條分隔線 */
}
.LogOrPsw {
  display: flex;
  flex-direction: column;
  align-items: center; /* 讓 label 和 input 也置中對齊 */
  gap: 8px; /* 控制帳號和密碼欄位之間的距離，取代原本的 margin-top */
}
.LogOrRes {
  display: flex;
  justify-content: center; /* 按鈕水平置中 */
  margin-top: 15px;
}

.member {
  color: white;
}

:root {
  --container: 1200px;
  --bg: #ffffff;
  --text: #222;
  --muted: #666;
  --border: #e9e9e9;
  --shadow: 0 10px 26px rgba(0, 0, 0, 0.08);
  --shadow-hover: 0 16px 40px rgba(0, 0, 0, 0.12);
  --brand: #111;
  --accent: #c7a36a;
  --radius: 14px;
}

body {
  margin: 0;
  padding: 0;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
    "Helvetica Neue", Arial, "Noto Sans TC", sans-serif;
  background-color: var(--bg);
  color: var(--text);
}

a {
  color: inherit;
}

/* 固定式導覽列 */
.navbar {
  position: sticky;
  top: 0;
  width: 100%;
  background: rgba(0, 0, 0, 0.88);
  backdrop-filter: blur(10px);
  border-bottom: 1px solid rgba(255, 255, 255, 0.08);
  z-index: 999;
}

.navbar-container {
  max-width: 1300px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 14px 20px;
  gap: 14px;
}

.brand {
  display: flex;
  align-items: center;
  gap: 12px;
  min-width: 220px;
}

.logo {
  color: white;
  font-size: 18px;
  font-weight: 800;
  letter-spacing: 1.5px;
  line-height: 1.1;
}

.logo small {
  display: block;
  font-size: 12px;
  font-weight: 600;
  letter-spacing: 2px;
  color: rgba(255, 255, 255, 0.7);
  margin-top: 4px;
}

.nav-links {
  display: flex;
  gap: 18px;
  flex-wrap: wrap;
  justify-content: flex-end;
}

.nav-links a {
  text-decoration: none;
  color: rgba(255, 255, 255, 0.9);
  font-size: 14px;
  padding: 8px 10px;
  border-radius: 10px;
  transition: background 0.2s ease, color 0.2s ease, border 0.2s ease;
  border: 1px solid transparent;
}

/* Navbar hover 改成金色系，不會變黑看不到 */
.nav-links a:hover {
  background: rgba(199, 163, 106, 0.18);
  color: #fff;
  text-decoration: none;
  border: 1px solid rgba(199, 163, 106, 0.35);
}

.nav-cta {
  display: flex;
  align-items: center;
  gap: 10px;
}
.nav-cta a {
  text-decoration: none;
  color: white;
}

.btn {
  border: 1px solid rgba(255, 255, 255, 0.18);
  background: transparent;
  color: #fff;
  padding: 9px 12px;
  border-radius: 12px;
  cursor: pointer;
  transition: transform 0.15s ease, background 0.2s ease;
  font-size: 14px;
  text-decoration: none;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
}

.btn:hover {
  background: rgba(255, 255, 255, 0.1);
  transform: translateY(-1px);
}

.btn-primary {
  border-color: rgba(199, 163, 106, 0.55);
  background: rgba(199, 163, 106, 0.15);
}
</style>
