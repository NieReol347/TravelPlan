<template>
  <div class="detail top-page" ref="detailRef">
    <tab-control
        v-if="showTabControl"
        class="tabs"
        :titles="names"
        @tabItemClick="tabClick"
        ref="tabControlRef"
    />
    <van-nav-bar
        title="房屋详情"
        left-text="旅途"
        left-arrow
        @click-left="onClickLeft"
    />
    <div class="main" v-if="mainPart" v-memo="[mainPart]">
      <detail-swipe :swipe-data="mainPart.topModule.housePicture.housePics"/>
      <detail-infos name="描述" :ref="getSectionRef" :top-infos="mainPart.topModule"/>
      <detail-facility name="设施" :ref="getSectionRef"
                       :house-facility="mainPart.dynamicModule.facilityModule.houseFacility"/>
      <detail-landlord name="房东" :ref="getSectionRef" :landlord="mainPart.dynamicModule.landlordModule"/>
      <detail-comment name="评论" :ref="getSectionRef" :comment="mainPart.dynamicModule.commentModule"/>
      <detail-notice name="须知" :ref="getSectionRef" :order-rules="mainPart.dynamicModule.rulesModule.orderRules"/>
      <!--      <detail-map name="周边" :ref="getSectionRef" :position="mainPart.dynamicModule.positionModule"/>-->
      <detail-intro :price-intro="mainPart.introductionModule"/>
      <DetailBuy/>
    </div>
    <div class="footer">
      <img src="@/assets/img/detail/icon_ensure.png" alt="">
      <div class="text">弘源旅途, 永无止境!</div>
    </div>

  </div>
</template>

<script setup>
import {ref, computed, watch} from 'vue'
import {useRoute, useRouter} from 'vue-router'
import {getDetailInfos} from "@/services"

import TabControl from "@/components/tab-control/tab-control.vue"
import DetailSwipe from "./cpns/detail_01-swipe.vue"
import DetailInfos from "./cpns/detail_02-infos.vue"
import DetailFacility from "./cpns/detail_03-facility.vue"
import DetailLandlord from "./cpns/detail_04-landlord.vue"
import DetailComment from "./cpns/detail_05-comment.vue"
import DetailNotice from "./cpns/detail_06-notice.vue"
import DetailMap from "./cpns/detail_07-map.vue"
import DetailIntro from "./cpns/detail_08-intro.vue"
import useScroll from '@/hooks/useScroll'
import DetailBuy from "@/views/detail/cpns/detail_09-buy.vue";


const router = useRouter()
const route = useRoute()
const houseId = route.params.id

// 发送网络请求获取数据
const detailInfos = ref({})
const mainPart = computed(() => detailInfos.value.mainPart)
getDetailInfos(houseId).then(res => {
  detailInfos.value = res.data
})

// 监听返回按钮的点击
const onClickLeft = () => {
  router.back()
}

// tabControl相关的操作
const detailRef = ref()
const {scrollTop} = useScroll(detailRef)
const showTabControl = computed(() => {
  return scrollTop.value >= 300
})

// const landlordRef = ref()
// const sectionEls = []
// const getSectionRef = (value) => {
//   sectionEls.push(value.$el)
// }

const sectionEls = ref({})
const names = computed(() => {
  return Object.keys(sectionEls.value)
})
const getSectionRef = (value) => {
  if (!value) return
  const name = value.$el.getAttribute("name")
  sectionEls.value[name] = value.$el
}

let tabClickFlag = false
let currentDistance = -1
const tabClick = (index) => {
  const key = Object.keys(sectionEls.value)[index]
  const el = sectionEls.value[key]
  let distance = el.offsetTop
  if (index !== 0) {
    distance = distance - 44
  }

  tabClickFlag = true
  currentDistance = distance


  detailRef.value.scrollTo({
    top: distance,
    behavior: "smooth"
  })
}

//滚动时，匹配对应的tabControl
const tabControlRef = ref()


watch(scrollTop, (newValue) => {
  if (newValue === currentDistance) {
    tabClickFlag = false
  }
  if (tabClickFlag) {
    //点击触发时，不要走滚动的逻辑
    return
  }

  // 1.获取每个部分的offsetTops
  // 如果添加了上面的点击不走滚动的逻辑，该部分不能放到外面。
  const els = Object.values(sectionEls.value)
  const values = els.map(el => el.offsetTop)

  // 2. 根据newValue去匹配索引
  let index = values.length - 1
  for (let i = 0; i < values.length; i++) {
    if (values[i] > newValue + 45) {
      index = i - 1
      break
    }
  }
  tabControlRef.value?.setCurrentIndex(index)
})

</script>

<style lang="less" scoped>
.tabs {
  position: fixed;
  z-index: 9;
  left: 0;
  right: 0;
  top: 0;
}

.footer {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 120px;

  img {
    width: 123px;
  }

  .text {
    margin-top: 12px;
    font-size: 12px;
    color: #7688a7;
  }
}
</style>
