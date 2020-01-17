<template>
  <div class="fw">
    <div id="wrapper">
      <div id="card_box" class="pr">
        <div id="cards">
          <div
            v-for="(card, $index) in cardArrs"
            :key="$index"
            class="card"
            :class="{ 'card-flipped': cardFlip }"
            ref="cards"
            :style="{ left: parseInt(cardWidth+13) * ($index%3) + 'px', top: parseInt(cardHeight+13) * Math.floor($index/3) + 'px' }"
            @click="clickCard(card['index'], $index)"
          >
            <div class="face front"></div>
            <div
              class="face back"
              :class="{ 'flip': cardFlip }"
              :style="{backgroundPosition: 'center center', backgroundSize: 'contain' }"
            >{{card['index']}}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="css">
@import "../assets/css/style";
</style>

<script>

// 查找是否包含某classname
var hasClass = (function () {
  var div = document.createElement("div");
  if ("classList" in div && typeof div.classList.contains === "function") {
    return function (elem, className) {
      return elem.classList.contains(className);
    };
  } else {
    return function (elem, className) {
      var classes = elem.className.split(/\s+/);
      for (var i = 0; i < classes.length; i++) {
        if (classes[i] === className) {
          return true;
        }
      }
      return false;
    };
  }
})();

export default {
  data () {
    return {
      cardArrs: [], // 卡牌列表
      cardWidth: 80, // 卡牌宽度
      cardHeight: 80, // 卡牌高度
      cardFlip: '', // 卡牌翻转的样式
      cardSelectedArr: [], // 临时存放当前点击的卡牌信息
      cardSelectedNum: 0, // 存放已点卡牌的数量
    }
  },
  mounted () {
    this.init(); // 初始化 
  },
  methods: {
    async init () {
      let self = this;
      // 重置卡牌选中的数量
      self.cardSelectedNum = 0;
      // 重置所有卡牌至背面
      self.cardFlip = '';
      // 加载卡牌列表（伪造数据）
      self.cardArrs = [
        {
          index: 0,
        },
        {
          value: 5,
        },
        {
          value: 1,
        },
        {
          value: 4,
        },
        {
          value: 2,
        },
        {
          value: 3,
        },
        {
          value: 1,
        },
        {
          value: 0,
        },
        {
          value: 5,
        },
        {
          value: 2,
        },
        {
          value: 4,
        },
        {
          value: 3,
        }
      ];

      // 重置JS添加的卡牌classname 待优化
      if (this.$refs.cards) {
        self.cardArrs.forEach(function (res, index) {
          self.$refs.cards[index].className = 'card';
        });
      }
    },
    clickCard (val, index) {
      // 点击卡牌
      // 游戏中
      var $fcards = this.$refs.cards;
      //  $fcards 是一个数组
      console.log($fcards);
      console.log('卡牌');
      var $fcard = $fcards[index];
      // 防止重复点击已点开的卡牌
      if (hasClass($fcard, "card-flipped")) {
        return;
      }
      console.log(val);
      // 记录点击的卡牌信息
      this.cardSelectedArr.push({ key: index, value: val });

      // 给卡牌添加classname
      $fcards.className = 'card';
      $fcard.className = 'card card-flipped';

      // 若翻动了两张牌，检测一致性
      if (this.cardSelectedArr.length == 2) {
        let self = this;
        setTimeout(function () {
          if (self.cardSelectedArr[0].value != self.cardSelectedArr[1].value) {
            // 两张卡牌不一致，重置
            self.cardSelectedArr.forEach(function (res) {
              $fcards[res.key].className = 'card';
            });
          } else {
            self.cardSelectedNum += 2; // 记录成功的卡牌数量
          }
          self.cardSelectedArr = []; // 清空数组，为了保证数组里最多存在2条数据
        }, 400);
      }
    }
  }
}
</script>
