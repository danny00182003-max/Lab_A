<template>
  <Toast />
  <div class="page">
    <!-- Template -->
    <div class="levelBox">
      <div class="levelTitle">會員等級儀表板</div>

      <div class="levelRow">
        <div class="levelItem">
          <div class="levelLabel">目前會員等級</div>
          <div class="levelValue">{{ member.currentLevel }}</div>
        </div>

        <div class="levelItem right">
          <div class="levelLabel">目前累積消費</div>
          <div class="levelValue big">
            {{ member.totalSpent.toLocaleString() }}
          </div>
        </div>
      </div>

      <div class="levelRow">
        <div class="levelLabel">距離下一級</div>

        <div v-if="!member.isMaxLevel" class="levelText">
          下一級：<b>{{ member.nextLevel }}</b
          >，還差
          <b>{{ member.remainingToNext.toLocaleString() }}</b>
        </div>

        <div v-else class="levelText">
          <b>已達最高等級</b>
        </div>
      </div>

      <div class="levelRow">
        <div class="levelLabel">進度</div>
        <ProgressBar :value="member.progressPercent" />
        <div class="levelHint">{{ member.progressPercent }}%</div>
      </div>
    </div>

    <div class="card">
      <div class="grid">
        <div class="field">
          <label>會員編號</label>
          <InputText v-model="form.userId" disabled />
        </div>

        <div class="field">
          <label>姓名</label>
          <InputText v-model="form.name" placeholder="請輸入姓名" />
          <Button label="儲存更新" icon="pi pi-save" @click="loadMemberInfo" />
        </div>

        <div class="field">
          <label>Email</label>
          <InputText v-model="form.email" placeholder="name@example.com" />
        </div>

        <div class="field">
          <label>手機</label>
          <InputText v-model="form.phone" placeholder="09xx-xxx-xxx" />
        </div>

        <div class="field full">
          <label>地址</label>
          <InputText v-model="form.address" placeholder="請輸入地址" />
        </div>
      </div>

      <div class="actions">
        <Button label="儲存更新" icon="pi pi-save" @click="save" />
        <Button
          label="重設"
          icon="pi pi-refresh"
          severity="secondary"
          @click="reset" />
      </div>

      <div v-if="message" class="message">{{ message }}</div>
    </div>
  </div>
</template>

<script setup>
  import { reactive, ref, onMounted } from "vue";
  import InputText from "primevue/inputtext";
  import Button from "primevue/button";
  import ProgressBar from "primevue/progressbar";
  import api from "../../api/api";
  import { useToast } from "primevue/usetoast";
  import { Toast } from "primevue";

  // 會員資料表單 ( 給RESET用 )
  const original = {
    userId: "MB-20260104-001",
    name: "王小明",
    email: "bbbbb@gmail.com",
    phone: "0912-345-678",
    address: "台中市西區XX路100號",
  };

  const apiData = ref(null); // 接API回傳的資料
  const form = reactive({ ...original });
  const message = ref("");
  const toast = useToast();

  async function loadMemberInfo() {
    try {
      const response = await api.get("/MemberInfo", {
        prams: { userId: form.userId },
      });
      apiData.value = response.data;
    } catch (error) {
      toast.add({
        severity: "error",
        summary: "錯誤",
        detail: "載入會員資料失敗。",
      });
    }
  }

  function save() {
    message.value = "已儲存（範例訊息）。";
  }

  function reset() {
    Object.assign(form, original);
    message.value = "";
  }

  // 消費進度條
  // Script (script setup)

  const member = ref({
    totalSpent: 36800,
    currentLevel: "Silver",
    nextLevel: "Gold",
    remainingToNext: 80000 - 36800,
    progressPercent:
      Math.round(100 * ((36800 - 30000) / (80000 - 30000)) * 10) / 10,
    isMaxLevel: false,
  });
</script>

<style scoped>
  .page {
    display: flex;
    flex-direction: column;
    gap: 12px;
  }

  .header h2 {
    margin: 0;
  }

  .desc {
    margin: 4px 0 0;
    color: #666;
  }
  .card {
    border: 1px solid #eee;
    border-radius: 12px;
    padding: 14px;
  }
  .grid {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 12px;
  }
  .field {
    display: flex;
    flex-direction: column;
    gap: 6px;
  }
  .field label {
    font-size: 13px;
    color: #444;
  }

  .full {
    grid-column: 1 / -1;
  }

  .actions {
    display: flex;
    gap: 8px;
    margin-top: 12px;
  }
  .message {
    margin-top: 10px;
    color: #1b5e20;
  }
  @media (max-width: 720px) {
    .grid {
      grid-template-columns: 1fr;
    }
  }
  /* 會員消費進度條 */
  /* CSS (scoped) */
  .levelBox {
    border: 1px solid #e6e6e6;
    border-radius: 12px;
    padding: 14px;
    background: #fff;
    margin-bottom: 16px;
  }

  .levelTitle {
    font-weight: 900;
    letter-spacing: 1px;
    margin-bottom: 12px;
  }

  .levelRow {
    display: grid;
    gap: 8px;
    margin-bottom: 12px;
  }

  .levelRow:last-child {
    margin-bottom: 0;
  }

  .levelItem {
    display: grid;
    gap: 4px;
  }

  .levelItem.right {
    text-align: right;
  }

  .levelLabel {
    color: #666;
    font-size: 12px;
  }

  .levelValue {
    font-size: 18px;
    font-weight: 900;
    color: #111;
  }

  .levelValue.big {
    font-size: 22px;
  }

  .levelText {
    color: #333;
    line-height: 1.6;
  }

  .levelHint {
    text-align: right;
    color: #666;
    font-size: 12px;
  }

  @media (min-width: 980px) {
    .levelRow:first-of-type {
      grid-template-columns: 1fr 1fr;
      align-items: end;
    }
  }
</style>
