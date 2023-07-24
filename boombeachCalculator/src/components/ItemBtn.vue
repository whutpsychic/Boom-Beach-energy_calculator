<template>
  <van-grid-item :class="greyType ? `item-can greying` : `item-can`">
    <div v-if="!empty" class="item-btn">
      <div class="item-btn-wrapper">
        <img class="op" ref="img" :src="judgeType(type)" />
      </div>
      <canvas ref="canvas" class="canvas" width="60" height="60" />
      <div class="item-btn-wrapper" v-show="greyType">
        <img ref="img2" class="greyType" :src="judgeType(type)" />
      </div>
      <div class="selected-can" v-show="selected">
        <el-icon :color="'green'">
          <Check />
        </el-icon>
      </div>
    </div>
    <div v-if="!empty" class="badger" v-show="!empty && (num > 0)">
      x{{ num }}
    </div>
    <div v-if="!empty" class="price" v-show="!empty && (energy > 0)">
      {{ energy === 999 ? '最大数量' : energy }}
    </div>
  </van-grid-item>
</template>

<script>
// 登陆兵种
import grenade from "@/assets/img/grenade.png"
import tank_fire from "@/assets/img/tank_fire.png"
import tank from "@/assets/img/tank.png"

// 战舰武器
import eggy from "@/assets/img/eggy.png"
import first_aid from "@/assets/img/first_aid.png"
import mecha from "@/assets/img/mecha.png"
import missile from "@/assets/img/missile.png"
import missile_multi from "@/assets/img/missile_multi.png"
import paralysis from "@/assets/img/paralysis.png"
import signal_flare from "@/assets/img/signal_flare.png"
import smoke from "@/assets/img/smoke.png"

// 英雄能力
import hero1_grenade from "@/assets/img/hero/hero1_grenade.png"
import hero1_roar from "@/assets/img/hero/hero1_roar.png"
import hero1_steel from "@/assets/img/hero/hero1_steel.png"
import hero2_healing from "@/assets/img/hero/hero2_healing.png"
import hero2_reborn from "@/assets/img/hero/hero2_reborn.png"
import hero2_shield from "@/assets/img/hero/hero2_shield.png"
import hero3_boomer from "@/assets/img/hero/hero3_boomer.png"
import hero3_eggy from "@/assets/img/hero/hero3_eggy.png"
import hero3_hacking from "@/assets/img/hero/hero3_hacking.png"
import hero4_comeon from "@/assets/img/hero/hero4_comeon.png"
import hero4_fucking from "@/assets/img/hero/hero4_fucking.png"
import hero4_hist from "@/assets/img/hero/hero4_hist.png"

// 附加能力
import hacker from "@/assets/img/limited/hacker.png"
import icer from "@/assets/img/limited/icer.png"
import paralysis_mini from "@/assets/img/limited/paralysis_mini.png"
import regeneration from "@/assets/img/limited/regeneration.png"
import serum from "@/assets/img/limited/serum.png"
import shield from "@/assets/img/limited/shield.png"
import super_aboriginal from "@/assets/img/limited/super_aboriginal.png"
import turret from "@/assets/img/limited/turret.png"
import UAV from "@/assets/img/limited/UAV.png"

// 限时武力
import big_yellow from "@/assets/img/prototype/big_yellow.png"
import chopper from "@/assets/img/prototype/chopper.png"
import tank_egg from "@/assets/img/prototype/tank_egg.png"
import tank_grenade from "@/assets/img/prototype/tank_grenade.png"
import tank_ice from "@/assets/img/prototype/tank_ice.png"
import tank_laser from "@/assets/img/prototype/tank_laser.png"

import { Check } from '@element-plus/icons-vue'

export default {
  components: { Check },
  props: {
    type: { type: String, default: "" },
    size: { type: Number, default: 60 },
    greyType: { type: Boolean, default: false },
    selected: { type: Boolean, default: false },
    empty: { type: Boolean, default: false },
    // 已使用次数
    num: { type: Number, default: 0 },
    // 下次使用将消耗能量数值
    energy: { type: Number, default: 0 },
  },
  data() {
    return {
      // 原始图片尺寸
      originalImgSize: 70,
    }
  },
  watch: {
    greyType(newv, oldv) {
      if ((newv !== oldv) && newv) {
        this.convertToGray()
      }
    }
  },
  methods: {
    // 主渲染
    judgeType(type) {
      switch (type) {
        // 登陆兵种
        case "grenade": return grenade
        case "tank_fire": return tank_fire
        case "tank": return tank

        // 战舰武器
        case "missile": return missile
        case "missile_multi": return missile_multi
        case "paralysis": return paralysis
        case "signal_flare": return signal_flare
        case "smoke": return smoke
        case "eggy": return eggy
        case "first_aid": return first_aid
        case "mecha": return mecha

        // 英雄能力
        case "hero1_grenade": return hero1_grenade
        case "hero1_roar": return hero1_roar
        case "hero1_steel": return hero1_steel
        case "hero2_healing": return hero2_healing
        case "hero2_reborn": return hero2_reborn
        case "hero2_shield": return hero2_shield
        case "hero3_boomer": return hero3_boomer
        case "hero3_eggy": return hero3_eggy
        case "hero3_hacking": return hero3_hacking
        case "hero4_comeon": return hero4_comeon
        case "hero4_fucking": return hero4_fucking
        case "hero4_hist": return hero4_hist

        // 附加能力
        case "hacker": return hacker
        case "icer": return icer
        case "paralysis_mini": return paralysis_mini
        case "regeneration": return regeneration
        case "serum": return serum
        case "shield": return shield
        case "super_aboriginal": return super_aboriginal
        case "turret": return turret
        case "UAV": return UAV

        // 限时武力
        case "big_yellow": return big_yellow
        case "chopper": return chopper
        case "tank_egg": return tank_egg
        case "tank_grenade": return tank_grenade
        case "tank_ice": return tank_ice
        case "tank_laser": return tank_laser

        default: return ""
      }
    },
    convertToGray() {
      const { type, judgeType, originalImgSize } = this
      let myImageNode = this.$refs.img
      let myImageNode2 = this.$refs.img2
      let canvas = this.$refs.canvas
      canvas.width = originalImgSize;
      canvas.height = originalImgSize;
      if (canvas.getContext) {
        let ctx = canvas.getContext("2d");
        myImageNode.onload = function () {
          ctx.drawImage(myImageNode, 0, 0);
          var length = originalImgSize * originalImgSize;
          let myImageData = ctx.getImageData(0, 0, originalImgSize, originalImgSize);
          for (var i = 0; i < length * 4; i += 4) {
            let myRed = myImageData.data[i];
            let myGreen = myImageData.data[i + 1];
            let myBlue = myImageData.data[i + 2];
            let myGray = parseInt((myRed + myGreen + myBlue) / 3);
            myImageData.data[i] = myGray;
            myImageData.data[i + 1] = myGray;
            myImageData.data[i + 2] = myGray;
          }
          ctx.putImageData(myImageData, 0, 0);
          let dataUrl = canvas.toDataURL()
          canvas.style.display = "none"
          myImageNode2.src = dataUrl
        }
        myImageNode.src = judgeType(type);
      }
    },
  }
}
</script>

<style scoped>
.item-can:active .item-btn {
  width: 50px;
  height: 50px;
}

.item-can.greying:active .item-btn {
  width: 60px;
  height: 60px;
}

.item-btn {
  position: relative;
  width: 60px;
  height: 60px;
  transition: .1s;
}

.item-btn img.op {
  width: 60px;
  height: 60px;
}

.item-btn img.greyType {
  position: absolute;
  top: 0;
  left: 0;
  width: 60px;
  height: 60px;
}

.item-btn-wrapper {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.selected-can {
  position: absolute;
  bottom: 0;
  right: 0;
  width: 20px;
  height: 20px;
  border: solid 1px green;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.badger {
  font-size: 20px;
  font-weight: bold;
  position: absolute;
  top: 4px;
  right: 4px;
  color: red;
}

.price {
  font-size: 17px;
  font-weight: bold;
  color: #888;
  position: absolute;
  bottom: 4px;
  left: 4px;
}
</style>