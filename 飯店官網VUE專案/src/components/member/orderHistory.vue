<template>
  <div class="page">
    <div class="header">
      <h2>歷史訂單</h2>
      <p class="desc">查看已完成或已入住的訂單紀錄。</p>
    </div>

    <div class="card">
      <div class="filters">
        <div class="field">
          <label>關鍵字（飯店/房型/訂單號）</label>
          <InputText
            v-model="keyword"
            placeholder="例如：Taichung / OD2025..."
          />
        </div>

        <div class="btns">
          <Button
            label="清除"
            icon="pi pi-times"
            severity="secondary"
            @click="keyword = ''"
          />
        </div>
      </div>

      <div class="divider"></div>

      <table class="table">
        <thead>
          <tr>
            <th>訂單編號</th>
            <th>飯店</th>
            <th>入住/退房</th>
            <th>房型</th>
            <th>狀態</th>
            <th class="right">金額</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="o in filtered" :key="o.orderNo">
            <td>{{ o.orderNo }}</td>
            <td>{{ o.hotel }}</td>
            <td>{{ o.checkIn }} ~ {{ o.checkOut }}</td>
            <td>{{ o.roomType }}</td>
            <td>{{ o.status }}</td>
            <td class="right">NT$ {{ o.total.toLocaleString() }}</td>
          </tr>

          <tr v-if="filtered.length === 0">
            <td colspan="6" class="empty">沒有符合的歷史訂單</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import { computed, ref } from "vue";
import InputText from "primevue/inputtext";
import Button from "primevue/button";

const keyword = ref("");

const history = ref([
  {
    orderNo: "OD20251231001",
    hotel: "範例飯店 Kaohsiung",
    checkIn: "2025-12-31",
    checkOut: "2026-01-01",
    roomType: "家庭房",
    status: "已入住",
    total: 9200,
  },
  {
    orderNo: "OD20251018007",
    hotel: "範例飯店 Taichung",
    checkIn: "2025-10-20",
    checkOut: "2025-10-21",
    roomType: "雙人房",
    status: "已完成",
    total: 3600,
  },
  {
    orderNo: "OD20250901003",
    hotel: "範例飯店 Taipei",
    checkIn: "2025-09-05",
    checkOut: "2025-09-06",
    roomType: "單人房",
    status: "已完成",
    total: 1800,
  },
]);

const filtered = computed(() => {
  const k = keyword.value.trim().toLowerCase();
  if (!k) return history.value;

  return history.value.filter((o) => {
    const text =
      `${o.orderNo} ${o.hotel} ${o.roomType} ${o.status}`.toLowerCase();
    return text.includes(k);
  });
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

.filters {
  display: grid;
  grid-template-columns: 1fr auto;
  gap: 12px;
  align-items: end;
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
.btns {
  display: flex;
  gap: 8px;
}
.divider {
  height: 1px;
  background: #eee;
  margin: 14px 0;
}

.table {
  width: 100%;
  border-collapse: collapse;
}
.table th,
.table td {
  border-bottom: 1px solid #f0f0f0;
  padding: 10px 8px;
  text-align: left;
}
.table th {
  font-size: 13px;
  color: #444;
  background: #fafafa;
}
.right {
  text-align: right;
}
.empty {
  text-align: center;
  color: #666;
  padding: 18px 8px;
}

@media (max-width: 720px) {
  .filters {
    grid-template-columns: 1fr;
  }
  .right {
    text-align: left;
  }
}
</style>
