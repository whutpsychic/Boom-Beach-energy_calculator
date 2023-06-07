<template>
  <header class="top-section">
    <div class="data-card">
      <p>战舰能量</p>
      <el-input-number v-model="myPower" :min="1" :max="999" @change="handleChange" />
    </div>
    <div class="data-card">
      <p>剩余能量</p>
      <span>{{ leftEnergy }}</span>
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
      <ItemBtn type="tank" @click="useItem('tank')" :num="tankNum" :energy="getNextCost(energyBase.energyTank, tankNum)"
        :greyType="cannotUseAgain(energyBase.energyTank, tankNum)" />
      <ItemBtn type="tank_fire" @click="useItem('tank_fire')" :num="trainNum"
        :energy="getNextCost(energyBase.energyTankFire, trainNum)"
        :greyType="cannotUseAgain(energyBase.energyTankFire, trainNum)" />
      <ItemBtn type="grenade" @click="useItem('grenade')" :num="oldManNum"
        :energy="getNextCost(energyBase.energyGrenade, oldManNum)"
        :greyType="cannotUseAgain(energyBase.energyGrenade, oldManNum)" />
      <ItemBtn v-if="prototypeArmy" :type="prototypeArmy" />
    </van-grid>
    <p>战舰武器</p>
    <van-grid :gutter="10" :column-num="4">
      <ItemBtn v-if="!weapon" empty />
      <ItemBtn v-else :type="weapon" @click="useItem('limited')" :num="limitedWeaponNum"
        :energy="getNextCost(limitedWeaponEnergy, limitedWeaponNum)"
        :greyType="cannotUseAgain(limitedWeaponEnergy, limitedWeaponNum)" />
      <ItemBtn type="eggy" @click="useItem('eggy')" :num="eggyNum" :energy="getNextCost(energyBase.energyEggy, eggyNum)"
        :greyType="cannotUseAgain(energyBase.energyEggy, eggyNum)" />
      <ItemBtn type="smoke" @click="useItem('smoke')" :num="smokeNum"
        :energy="getNextCost(energyBase.energySmoke, smokeNum)"
        :greyType="cannotUseAgain(energyBase.energySmoke, smokeNum)" />
      <ItemBtn type="missile_multi" @click="useItem('missile_multi')" :num="missileMultiNum"
        :energy="getNextCost(energyBase.energyMissileMulti, missileMultiNum)"
        :greyType="cannotUseAgain(energyBase.energyMissileMulti, missileMultiNum)" />
      <ItemBtn type="paralysis" @click="useItem('paralysis')" :num="paralysisNum"
        :energy="getNextCost(energyBase.energyParalysis, paralysisNum)"
        :greyType="cannotUseAgain(energyBase.energyParalysis, paralysisNum)" />
      <ItemBtn type="first_aid" @click="useItem('first_aid')" :num="firstAidNum"
        :energy="getNextCost(energyBase.energyFirstAid, firstAidNum)"
        :greyType="cannotUseAgain(energyBase.energyFirstAid, firstAidNum)" />
      <ItemBtn type="signal_flare" @click="useItem('signal_flare')" :num="signalNum"
        :energy="getNextCost(energyBase.energySignalFlare, signalNum)"
        :greyType="cannotUseAgain(energyBase.energySignalFlare, signalNum)" />
      <ItemBtn type="missile" @click="useItem('missile')" :num="missileNum"
        :energy="getNextCost(energyBase.energyMissile, missileNum)"
        :greyType="cannotUseAgain(energyBase.energyMissile, missileNum)" />
    </van-grid>
    <p>英雄技能</p>
    <van-grid :gutter="10" :column-num="4">
      <ItemBtn v-if="!heroPower" empty />
      <ItemBtn v-else :type="heroPower" @click="useItem('hero')" :num="heroNum" :energy="getNextCost(heroEnergy, heroNum)"
        :greyType="cannotUseAgain(heroEnergy, heroNum)" />
    </van-grid>
    <div :style="'height:30px;'" />
    <van-button block @click="resetValues">重 来</van-button>
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
import * as energyBase from "../dataBase"

export default {
  components: { ItemBtn, ItemTag },
  data() {
    return {
      // 图标组件
      Switch,
      energyBase,
      // 能量数值
      myPower: 110,
      cost: 0,
      // =============================
      // 英雄能力
      // heroPower: "hero1_grenade",
      heroPower: null,
      // 附加能力
      weapon: null,
      // 原型部队
      prototypeArmy: null,
      // =============================
      // 导弹数量
      missileNum: 0,
      // 信号弹数量
      signalNum: 0,
      // 医疗包数量
      firstAidNum: 0,
      // 震爆数量
      paralysisNum: 0,
      // 多管数量
      missileMultiNum: 0,
      // 烟雾数量
      smokeNum: 0,
      // 小怪数量
      eggyNum: 0,
      // 限时武器数量
      limitedWeaponNum: 0,
      // 限时武器能量数据
      limitedWeaponEnergy: [],

      // 坦克数量
      tankNum: 0,
      // 火车数量
      trainNum: 0,
      // 老头数量
      oldManNum: 0,
      // 原型兵种数量
      originalNum: 0,
      // 原型兵种能量数据
      originalEnergy: [],

      // 英雄技能数量
      heroNum: 0,
      // 英雄能量数据
      heroEnergy: [],
      // =============================
      // 控制台 - 显示抽屉
      showDrawer: false,
    }
  },
  computed: {
    leftEnergy() {
      const { myPower, cost } = this
      return myPower - cost
    }
  },
  mounted() {

  },
  methods: {
    chooseWeapon(weapon) {
      this.weapon = weapon
      switch (weapon) {
        case "hacker":
          this.limitedWeaponEnergy = energyBase.hacker
          break;
        case "icer":
          this.limitedWeaponEnergy = energyBase.icer
          break;
        case "paralysis_mini":
          this.limitedWeaponEnergy = energyBase.paralysis_mini
          break;
        case "regeneration":
          this.limitedWeaponEnergy = energyBase.regeneration
          break;
        case "serum":
          this.limitedWeaponEnergy = energyBase.serum
          break;
        case "shield":
          this.limitedWeaponEnergy = energyBase.shield
          break;
        case "super_aboriginal":
          this.limitedWeaponEnergy = energyBase.super_aboriginal
          break;
        case "turret":
          this.limitedWeaponEnergy = energyBase.turret
          break;
        default: return
      }
    },
    choosePrototype(prototype) {
      console.log(prototype)
      this.prototypeArmy = prototype
      switch (prototype) {
        case "xxxxxx":
          this.sssssss = energyBase.xxxxxx
          break;
        default: return
      }
    },
    chooseHero(hero) {
      this.heroPower = hero
      switch (hero) {
        case "hero1_grenade":
          this.heroEnergy = energyBase.hero1_1
          break;
        case "hero1_steel":
          this.heroEnergy = energyBase.hero1_2
          break;
        case "hero1_roar":
          this.heroEnergy = energyBase.hero1_3
          break;

        case "hero2_healing":
          this.heroEnergy = energyBase.hero2_1
          break;
        case "hero2_shield":
          this.heroEnergy = energyBase.hero2_2
          break;
        case "hero2_reborn":
          this.heroEnergy = energyBase.hero2_3
          break;

        case "hero3_eggy":
          this.heroEnergy = energyBase.hero3_1
          break;
        case "hero3_boomer":
          this.heroEnergy = energyBase.hero3_2
          break;
        case "hero3_hacking":
          this.heroEnergy = energyBase.hero3_3
          break;

        case "hero4_comeon":
          this.heroEnergy = energyBase.hero4_1
          break;
        case "hero4_fucking":
          this.heroEnergy = energyBase.hero4_2
          break;
        case "hero4_hist":
          this.heroEnergy = energyBase.hero4_3
          break;
        default: return
      }
    },
    onChooseComplete() {
      this.resetValues()
      this.showDrawer = false
    },
    // 消耗能量
    costEnery(n) {
      this.cost += n;
    },
    // 计算下次使用武器的能量消耗
    getNextCost(energy, num) {
      const L = energy.length
      const opL = num >= L ? L - 1 : num
      return energy[opL]
    },
    // 不能再次使用
    cannotUseAgain(energy, num) {
      const { leftEnergy } = this
      let willCost = this.getNextCost(energy, num)
      if (willCost > leftEnergy) {
        return true
      } else {
        return false
      }
    },
    // 使用物品
    useItem(type) {
      const { energyMissile, energySignalFlare, energyFirstAid, energyParalysis, energyMissileMulti, energySmoke, energyEggy } = energyBase
      const { energyTank, energyTankFire, energyGrenade } = energyBase
      const { leftEnergy } = this
      let willCost = 0
      switch (type) {
        // 基础兵力
        case "missile":
          const { missileNum } = this
          willCost = this.getNextCost(energyMissile, missileNum)
          if (willCost > leftEnergy) {
            return
          }
          this.costEnery(willCost)
          this.missileNum += 1;
          break;
        case "signal_flare":
          const { signalNum } = this
          willCost = this.getNextCost(energySignalFlare, signalNum)
          if (willCost > leftEnergy) {
            return
          }
          this.costEnery(willCost)
          this.signalNum += 1;
          break;
        case "first_aid":
          const { firstAidNum } = this
          willCost = this.getNextCost(energyFirstAid, firstAidNum)
          if (willCost > leftEnergy) {
            return
          }
          this.costEnery(willCost)
          this.firstAidNum += 1;
          break;
        case "paralysis":
          const { paralysisNum } = this
          willCost = this.getNextCost(energyParalysis, paralysisNum)
          if (willCost > leftEnergy) {
            return
          }
          this.costEnery(willCost)
          this.paralysisNum += 1;
          break;
        case "missile_multi":
          const { missileMultiNum } = this
          willCost = this.getNextCost(energyMissileMulti, missileMultiNum)
          if (willCost > leftEnergy) {
            return
          }
          this.costEnery(willCost)
          this.missileMultiNum += 1;
          break;
        case "smoke":
          const { smokeNum } = this
          willCost = this.getNextCost(energySmoke, smokeNum)
          if (willCost > leftEnergy) {
            return
          }
          this.costEnery(willCost)
          this.smokeNum += 1;
          break;
        case "eggy":
          const { eggyNum } = this
          willCost = this.getNextCost(energyEggy, eggyNum)
          if (willCost > leftEnergy) {
            return
          }
          this.costEnery(willCost)
          this.eggyNum += 1;
          break;
        // 
        case "grenade":
          const { oldManNum } = this
          willCost = this.getNextCost(energyGrenade, oldManNum)
          if (willCost > leftEnergy) {
            return
          }
          this.costEnery(willCost)
          this.oldManNum += 1;
          break;
        case "tank_fire":
          const { trainNum } = this
          willCost = this.getNextCost(energyTankFire, trainNum)
          if (willCost > leftEnergy) {
            return
          }
          this.costEnery(willCost)
          this.trainNum += 1;
          break;
        case "tank":
          const { tankNum } = this
          willCost = this.getNextCost(energyTank, tankNum)
          if (willCost > leftEnergy) {
            return
          }
          this.costEnery(willCost)
          this.tankNum += 1;
          break;
        // 英雄技能
        case "hero":
          const { heroNum, heroEnergy } = this
          willCost = this.getNextCost(heroEnergy, heroNum)
          if (willCost > leftEnergy) {
            return
          }
          this.costEnery(willCost)
          this.heroNum += 1;
          break;
        // 原型兵种
        case "limited":
          const { limitedWeaponNum, limitedWeaponEnergy } = this
          willCost = this.getNextCost(limitedWeaponEnergy, limitedWeaponNum)
          if (willCost > leftEnergy) {
            return
          }
          this.costEnery(willCost)
          this.limitedWeaponNum += 1;
          break;
        // 限时武器

        default: return
      }
    },
    // 数值复位
    resetValues() {
      this.cost = 0
      this.missileNum = 0
      this.signalNum = 0
      this.firstAidNum = 0
      this.paralysisNum = 0
      this.missileMultiNum = 0
      this.smokeNum = 0
      this.eggyNum = 0
      this.tankNum = 0
      this.trainNum = 0
      this.oldManNum = 0
      // 
      this.heroNum = 0
      this.originalNum = 0
      this.limitedWeaponNum = 0
      // this.heroEnergy = []
      // this.originalEnergy = []
      // this.limitedWeaponEnergy = []
    },
    // // 设置环境(英雄技能、限时能力、原型兵种)
    // setEnv() {
    //   // 
    //   this.heroNum = 0
    //   this.originalNum = 0
    //   this.limitedWeaponNum = 0
    //   this.heroEnergy = []
    //   this.originalEnergy = []
    //   this.limitedWeaponEnergy = []
    // },
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
  top: calc(50%);
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
