<template>
  <header class="top-section">
    <div class="data-card">
      <p>战舰能量</p>
      <el-input-number v-model="myPower" :min="1" :max="999" @change="handleChange" />
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
  <main class="main-body">
    <div class="affixer" v-show="!showDrawer">
      <el-button :icon="Switch" round size="large" @click="showDrawer = true"></el-button>
    </div>
    <p>登陆兵力</p>
    <van-grid :gutter="10" :column-num="4">
      <ItemBtn type="tank" />
      <ItemBtn type="tank_fire" />
      <ItemBtn type="grenade" />
      <ItemBtn v-if="prototypeArmy" :type="prototypeArmy" />
    </van-grid>
    <p>战舰武器</p>
    <van-grid :gutter="10" :column-num="4">
      <ItemBtn v-if="!weapon" empty />
      <ItemBtn v-else :type="weapon" />
      <ItemBtn type="eggy" />
      <ItemBtn type="smoke" />
      <ItemBtn type="missile_multi" />
      <ItemBtn type="paralysis" />
      <ItemBtn type="first_aid" />
      <ItemBtn type="signal_flare" />
      <ItemBtn type="missile" />
    </van-grid>
    <p>英雄技能</p>
    <van-grid :gutter="10" :column-num="4">
      <ItemBtn :type="heroPower" />
    </van-grid>
  </main>
  <!-- 选择武器抽屉 -->
  <el-drawer v-model="showDrawer" :direction="'ltr'" :size="'95%'" :show-close="false" :with-header="false">
    <p class="drawer-title"><span>状态配置</span><el-button @click="onChooseComplete" :size="'large'">ok</el-button></p>
    <p>英雄能力</p>
    <van-grid :gutter="10" :column-num="3">
      <ItemTag type="hero1_grenade" @click="chooseHero('hero1_grenade')"
        :greyType="heroPower && (heroPower !== 'hero1_grenade')" :selected="heroPower === 'hero1_grenade'" />
      <ItemTag type="hero1_steel" @click="chooseHero('hero1_steel')"
        :greyType="heroPower && (heroPower !== 'hero1_steel')" :selected="heroPower === 'hero1_steel'" />
      <ItemTag type="hero1_roar" @click="chooseHero('hero1_roar')" :greyType="heroPower && (heroPower !== 'hero1_roar')"
        :selected="heroPower === 'hero1_roar'" />
      <ItemTag type="hero2_healing" @click="chooseHero('hero2_healing')"
        :greyType="heroPower && (heroPower !== 'hero2_healing')" :selected="heroPower === 'hero2_healing'" />
      <ItemTag type="hero2_shield" @click="chooseHero('hero2_shield')"
        :greyType="heroPower && (heroPower !== 'hero2_shield')" :selected="heroPower === 'hero2_shield'" />
      <ItemTag type="hero2_reborn" @click="chooseHero('hero2_reborn')"
        :greyType="heroPower && (heroPower !== 'hero2_reborn')" :selected="heroPower === 'hero2_reborn'" />
      <ItemTag type="hero3_eggy" @click="chooseHero('hero3_eggy')" :greyType="heroPower && (heroPower !== 'hero3_eggy')"
        :selected="heroPower === 'hero3_eggy'" />
      <ItemTag type="hero3_boomer" @click="chooseHero('hero3_boomer')"
        :greyType="heroPower && (heroPower !== 'hero3_boomer')" :selected="heroPower === 'hero3_boomer'" />
      <ItemTag type="hero3_hacking" @click="chooseHero('hero3_hacking')"
        :greyType="heroPower && (heroPower !== 'hero3_hacking')" :selected="heroPower === 'hero3_hacking'" />
      <ItemTag type="hero4_comeon" @click="chooseHero('hero4_comeon')"
        :greyType="heroPower && (heroPower !== 'hero4_comeon')" :selected="heroPower === 'hero4_comeon'" />
      <ItemTag type="hero4_fucking" @click="chooseHero('hero4_fucking')"
        :greyType="heroPower && (heroPower !== 'hero4_fucking')" :selected="heroPower === 'hero4_fucking'" />
      <ItemTag type="hero4_hist" @click="chooseHero('hero4_hist')" :greyType="heroPower && (heroPower !== 'hero4_hist')"
        :selected="heroPower === 'hero4_hist'" />
    </van-grid>
    <p>附加能力</p>
    <van-grid :gutter="10" :column-num="3">
      <ItemTag type="hacker" @click="chooseWeapon('hacker')" :greyType="weapon && (weapon !== 'hacker')"
        :selected="weapon === 'hacker'" />
      <ItemTag type="icer" @click="chooseWeapon('icer')" :greyType="weapon && (weapon !== 'icer')"
        :selected="weapon === 'icer'" />
      <ItemTag type="paralysis_mini" @click="chooseWeapon('paralysis_mini')"
        :greyType="weapon && (weapon !== 'paralysis_mini')" :selected="weapon === 'paralysis_mini'" />
      <ItemTag type="regeneration" @click="chooseWeapon('regeneration')" :greyType="weapon && (weapon !== 'regeneration')"
        :selected="weapon === 'regeneration'" />
      <ItemTag type="serum" @click="chooseWeapon('serum')" :greyType="weapon && (weapon !== 'serum')"
        :selected="weapon === 'serum'" />
      <ItemTag type="shield" @click="chooseWeapon('shield')" :greyType="weapon && (weapon !== 'shield')"
        :selected="weapon === 'shield'" />
      <ItemTag type="super_aboriginal" @click="chooseWeapon('super_aboriginal')"
        :greyType="weapon && (weapon !== 'super_aboriginal')" :selected="weapon === 'super_aboriginal'" />
      <ItemTag type="turret" @click="chooseWeapon('turret')" :greyType="weapon && (weapon !== 'turret')"
        :selected="weapon === 'turret'" />
      <!-- <ItemTag type="UAV" @click="chooseWeapon('UAV')" :greyType="weapon && (weapon !== 'UAV')"
        :selected="weapon === 'UAV'" /> -->
    </van-grid>
    <p>限时武力</p>
    <van-grid :gutter="10" :column-num="3">
      <ItemTag type="big_yellow" @click="choosePrototype('big_yellow')"
        :greyType="prototypeArmy && (prototypeArmy !== 'big_yellow')" :selected="prototypeArmy === 'big_yellow'" />
      <ItemTag type="chopper" @click="choosePrototype('chopper')"
        :greyType="prototypeArmy && (prototypeArmy !== 'chopper')" :selected="prototypeArmy === 'chopper'" />
      <ItemTag type="tank_egg" @click="choosePrototype('tank_egg')"
        :greyType="prototypeArmy && (prototypeArmy !== 'tank_egg')" :selected="prototypeArmy === 'tank_egg'" />
      <ItemTag type="tank_grenade" @click="choosePrototype('tank_grenade')"
        :greyType="prototypeArmy && (prototypeArmy !== 'tank_grenade')" :selected="prototypeArmy === 'tank_grenade'" />
      <ItemTag type="tank_ice" @click="choosePrototype('tank_ice')"
        :greyType="prototypeArmy && (prototypeArmy !== 'tank_ice')" :selected="prototypeArmy === 'tank_ice'" />
      <ItemTag type="tank_laser" @click="choosePrototype('tank_laser')"
        :greyType="prototypeArmy && (prototypeArmy !== 'tank_laser')" :selected="prototypeArmy === 'tank_laser'" />
    </van-grid>
  </el-drawer>
</template>

<script>
import { Switch } from '@element-plus/icons-vue'
import ItemBtn from '../components/ItemBtn.vue';
import ItemTag from '../components/ItemTag.vue';

export default {
  components: { ItemBtn, ItemTag },
  data() {
    return {
      // 图标组件
      Switch,
      // 能量数值
      myPower: 68,
      cost: 0,
      // 英雄能力
      heroPower: "hero1_grenade",
      // 附加能力
      weapon: null,
      // 原型部队
      prototypeArmy: null,
      // =============================
      // 控制台 - 显示抽屉
      showDrawer: true,
    }
  },
  computed: {
    left() {
      const { myPower, cost } = this
      return myPower - cost
    }
  },
  mounted() {

  },
  methods: {
    chooseWeapon(weapon) {
      this.weapon = weapon
    },
    choosePrototype(prototype) {
      this.prototypeArmy = prototype
    },
    chooseHero(hero) {
      this.heroPower = hero
    },
    onChooseComplete() {
      this.showDrawer = false
    },
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

.affixer {
  position: fixed;
  top: calc(50% - 16px);
  left: -16px;
  width: 32px;
  height: 32px;
  z-index: 99;
}

.drawer-title {
  font-weight: bold;
  font-size: 18px;
  color: #666;
  margin-bottom: 2em;
  display: flex;
  justify-content: space-between;
}

.item-btn {
  width: 70px;
  height: 70px;
}
</style>
