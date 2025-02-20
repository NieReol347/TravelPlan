<script setup>

</script>

<template>
  <div class="buy">
    <div class="chat">
      <img src="@/assets/img/detail/icon_chat.png" alt="">
      <div>聊天</div>
    </div>

    <div class="price">
      <div>￥2000</div>
    </div>

    <div class="btn">
      <img src="@/assets/img/detail/icon_order.png" alt="">
      <div class="text">预订当前房源</div>
    </div>

  </div>
</template>

<style scoped lang="less">
.buy {
  display: flex;
  justify-content: space-between;
  align-items: center;

  .chat {
    img {
      width: 20px;
      height: 20px;
    }
  }

  .price {

  }

  .btn {
    .text {
      line-height: 1.5;
    }
  }
}
</style>