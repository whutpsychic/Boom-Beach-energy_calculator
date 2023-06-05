<template>
  <header class="top-section">
    <div class="data-card">
      <p>战舰能量</p>
      <el-input-number v-model="num" :min="1" :max="999" @change="handleChange" />
    </div>
    <div class="data-card">
      <p>剩余能量</p>
      <span>{{ left }}</span>
    </div>
    <div class="data-card">
      <p>消耗能量</p>
      <span>{{ cost }}</span>
    </div>
  </header>
  <main>
  </main>
</template>

<script>


export default {
  data() {
    return {
      myPower: 0,
      left: 0,
      cost: 0,
    }
  },
  mounted() {

  },
}

</script>

<style scoped>
.top-section {
  display: flex;
  justify-content: space-around;
  align-items: center;
}

.data-card {
  border: solid 1px #ccc;
  width: 30%;
  flex-basis: 30%;
  height: 50px;
  padding-top: 10px;
  padding-bottom: 10px;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-content: space-between;
  border-radius: 8px;
  box-shadow: 2px 2px 4px 4px #ddd;
}

.data-card p {
  text-align: center;
  flex-basis: 100%;
  margin: 0;
}
</style>
