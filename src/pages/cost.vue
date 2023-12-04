<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div class="cost">
    <div class="cost-inputs">
      <Input
        :props-label="t('pages.profit.materialPrice')"
        v-model="params.materialPrice"
      />
      <Input
        :props-label="t('pages.profit.materialQuantity')"
        v-model="params.materialQuantity"
      />
      <Input
        :props-label="t('pages.profit.processedProductPrice')"
        v-model="params.processedProductPrice"
      />
      <Input
        :props-label="t('pages.profit.itemValue')"
        v-model="params.itemValue"
      />
      <Input
        :props-label="t('pages.profit.serviceCharge')"
        v-model="params.serviceCharge"
      />
      <Input
        :props-label="t('pages.profit.shippingCost')"
        v-model="params.shippingCost"
      />
      <Input
        :props-label="t('pages.profit.sellingPrice')"
        v-model="params.sellingPrice"
      />
      <Input
        :props-label="t('pages.profit.returnPersent')"
        v-model="params.returnPersent"
      />
    </div>
    <div class="cost-outputs">
      <div class="cost-submit submit-btn" @click="onSubmit">
        {{ t("button.count") }}
      </div>
      <div>{{ t("pages.profit.cost.cost") }}:{{ result.cost }}</div>
      <div>{{ t("pages.profit.cost.fee") }}:{{ result.fee }}</div>
      <div>{{ t("pages.profit.profit") }}:{{ result.profit }}</div>
    </div>
  </div>
</template>
<script>
import { reactive } from "vue";
import useI18n from "@/hooks/use-i18n";
// import { useAlert } from "@/hooks/use-alert";
/** components */
import Input from "@/widgets/input.vue";
import { useAlert } from "@/hooks/use-alert";

export default {
  layout: "layout-backend",
  components: {
    Input,
  },
  setup() {
    const { t } = useI18n();
    const swal = useAlert();
    const result = reactive({
      fee: "",
      profit: "",
      cost: "",
    });

    const params = reactive({
      materialPrice: 10, // 主加工品價格
      materialQuantity: 5, // 主加工品數量
      processedProductPrice: 20, // 次加工品價格
      itemValue: 100, // 物品價值
      serviceCharge: 10, // 商家價格
      shippingCost: 5, // 旅行補正費用(運費)
      sellingPrice: 150, // 販售價格
      returnPersent: 40, // 返還%數
    });

    function calculateCostAndProfit(params) {
      const {
        materialPrice,
        materialQuantity,
        processedProductPrice,
        itemValue,
        serviceCharge,
        shippingCost,
        sellingPrice,
        returnPersent,
      } = params;

      /**
       * 手續費
       * @desc 食物消耗 = 物品價值 * 0.1125
       *       使用費 = 食物消耗 / 100 * 你訂的價格 + 旅行花費補正
       */
      const foodConsumption = itemValue * 0.1125;
      const handlingFee =
        (foodConsumption / 100) * serviceCharge + shippingCost; // 加工費

      /**
       * 材料返還率
       * @desc 返還的錢 = 材料價格 * 返還百分比 / 100
       *       返還成本 = 原本材料價格 - 返還的錢
       */
      const materialTotalPrice =
        materialPrice * materialQuantity + processedProductPrice;
      const returnPrice = (materialTotalPrice * returnPersent) / 100;

      /**
       * 成本及利潤
       * @desc 成本 = 主加工品價格 * 數量 + 次加工品價格 + 加工費 - 材料返還率
       *       利潤 = 販售金額 - 成本
       */

      /** 成本 */
      const cost =
        materialPrice * materialQuantity +
        processedProductPrice +
        handlingFee -
        returnPrice;

      /** 利潤 */
      const profit = sellingPrice - cost;

      return { cost, profit, handlingFee };
    }

    function checkAndConvertToNumber(params) {
      for (const [key, value] of Object.entries(params)) {
        // 嘗試轉換為數字
        const numberValue = Number(value);

        // 檢查是否轉換為NaN
        if (isNaN(numberValue)) {
          return key; // 轉換失敗，回傳變數名稱
        }

        // 更新參數值為數字型別
        params[key] = numberValue;
      }
      return "pass"; // 所有參數都成功轉換為數字
    }

    const onSubmit = () => {
      if (checkAndConvertToNumber(params) !== "pass") {
        swal.alert({
          title: t("pages.profit.error.title"),
          text: t("pages.profit.error.text2"),
        });
        return;
      }
      const { cost, profit, handlingFee } = calculateCostAndProfit(params);
      result.cost = cost;
      result.fee = handlingFee;
      result.profit = profit;
    };

    return {
      result,
      params,
      t,
      onSubmit,
      calculateCostAndProfit,
    };
  },
};
</script>
