<template>
  <section>
    <SubscribeStepProgress :currentStep="3" />
    <div class="subscribe-success">
      <div class="subscribe-success__message">
        {{ successMessage }}
      </div>
      <div class="subscribe-success__info">
        <h1 class="subscribe-success__info_title">訂單資訊</h1>
        <SubscribeSuccessOrderInfoContentRow
          title="訂單編號"
          :data="orderInfo.orderId"
        />
        <SubscribeSuccessOrderInfoContentRow
          v-if="orderInfo.discountPrice"
          title="優惠折扣碼"
          :data="orderInfo.discountPrice"
        />
        <!--        <SubscribeSuccessOrderInfoContentRow-->
        <!--          title="訂閱起訖"-->
        <!--          :data="orderInfo.during"-->
        <!--          class="during"-->
        <!--        />-->
        <div class="subscribe-success__info_row">
          <div class="subscribe-success__info_row_title">訂閱方案</div>
          <div class="subscribe-success__info_row_data">
            <MembershipFormPerchaseInfo
              :perchasedPlan="perchasedList"
              :showTitle="false"
            />
          </div>
        </div>
        <div class="subscribe-success__info_button">
          <a
            href="/profile/purchase"
            @click.once="
              useCustomEventToFbPixel('back-to-profile-purchase-page')
            "
          >
            <UiMembershipButtonSecondary>
              <p>回訂閱紀錄看購買文章</p>
            </UiMembershipButtonSecondary>
          </a>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import SubscribeStepProgress from '~/components/SubscribeStepProgress.vue'
import SubscribeSuccessOrderInfoContentRow from '~/components/SubscribeSuccessOrderInfoContentRow.vue'
import MembershipFormPerchaseInfo from '~/components/MembershipFormPerchaseInfo.vue'
import UiMembershipButtonSecondary from '~/components/UiMembershipButtonSecondary.vue'
import { useCustomEventToFbPixel } from '~/composition/fb-pixel.js'
export default {
  middleware: ['handle-go-to-marketing'],
  setup() {
    return {
      useCustomEventToFbPixel,
    }
  },
  components: {
    SubscribeStepProgress,
    SubscribeSuccessOrderInfoContentRow,
    MembershipFormPerchaseInfo,
    UiMembershipButtonSecondary,
  },
  data() {
    return {
      orderInfo: {
        orderId: this.$route.query.orderNumber ?? '',
      },

      // TODO: remove due to not use anymore
      hasLink: false,
      isUpgradeFromMonthToYear: true,
    }
  },
  computed: {
    perchasedList() {
      switch (this.$route.query.code) {
        case 'one_time': {
          return [
            {
              detail: '鏡週刊Basic會員（單篇）',
              hint: '單篇 $1 元，享 14 天內無限次觀看',
              price: '原價 NT$1',
              newPrice: 1,
              key: 'basic',
            },
          ]
        }
        case 'monthly': {
          return [
            {
              detail: '鏡週刊Premium會員（月方案）',
              hint: '每月 $49 元，信用卡自動續扣',
              price: '原價 NT$99',
              newPrice: 49,
              key: 'month',
            },
          ]
        }
        case 'yearly': {
          return [
            {
              detail: '鏡週刊Premium會員（年方案）',
              hint: '每年 $499 元，信用卡自動續扣',
              price: '原價 NT$1188',
              newPrice: 499,
              key: 'year',
            },
          ]
        }
        default: {
          return [{}]
        }
      }
    },
    successMessage() {
      if (this.isUpgradeFromMonthToYear) {
        return '訂單處理中，請稍候在訂閱紀錄確認訂單狀況。'
      } else {
        return '您已完成付款，以下為本次訂購資訊，已同步寄送至您的信箱。'
      }
    },
  },
  methods: {
    // TODO: remove due to not use anymore
    toggleHasLink() {
      this.hasLink = !this.hasLink
    },
  },
}
</script>

<style lang="scss" scoped>
.subscribe-success {
  max-width: 960px;
  margin: 0 auto;
  padding: 20px 8px;
  color: rgba(0, 0, 0, 0.87);
  @include media-breakpoint-up(sm) {
    padding: 48px 5px;
    font-size: 18px;
    line-heipht: 27px;
  }

  &__message {
    margin: 0 16px;
    margin-bottom: 20px;
    font-size: 16px;
    line-height: 150%;
    @include media-breakpoint-up(sm) {
      margin: 0 24px 24px 24px;
      font-size: 18px;
    }
  }

  &__info {
    padding: 16px;
    background: #f5f5f5;
    border-radius: 4px;
    @include media-breakpoint-up(sm) {
      padding: 24px;
    }

    &_title {
      margin-bottom: 24px;
      font-size: 22px;
      line-height: 31px;
      @include media-breakpoint-up(sm) {
        font-size: 24px;
        line-height: 34px;
      }
    }

    &_row {
      margin-top: 24px;
      margin-bottom: 51px;
      @include media-breakpoint-up(sm) {
        display: flex;
        margin-top: 12px;
        margin-bottom: 24px;
      }

      &_title {
        @include media-breakpoint-up(sm) {
          min-width: 180px;
        }
      }

      &_data {
        flex: 1;

        .perchase-info {
          padding: 0px;
          margin-top: 4px;
          @include media-breakpoint-up(sm) {
            margin-top: 0;
          }
        }
      }
    }

    // .during {
    //   flex-direction: column;
    //   gap: 4px;
    //   margin-top: 24px;
    //   @include media-breakpoint-up(sm) {
    //     flex-direction: row;
    //     gap: 0px;
    //     margin-top: 12px;
    //   }
    // }

    &_button {
      display: flex;
      justify-content: center;
      flex-direction: column;
      @include media-breakpoint-up(sm) {
        flex-direction: row;
      }
      & > * + * {
        margin-top: 12px;
        @include media-breakpoint-up(sm) {
          margin-top: 0;
          margin-left: 12px;
        }
      }
      .subcribe-button,
      .button {
        width: 240px;
        height: 48px;
        padding-top: 12px;
        margin: 0 auto;
        @include media-breakpoint-up(sm) {
          margin: 0;
        }
      }
    }
  }

  ::v-deep .content_row__title {
    min-width: 100px;
    @include media-breakpoint-up(sm) {
      min-width: 180px;
    }
  }
}
</style>
