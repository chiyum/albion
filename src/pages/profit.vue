<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div class="profit">
    <div class="profit-inputs">
      <div
        class="profit-inputs-block"
        v-for="(user, index) in inputCol"
        :key="`user-${index}`"
      >
        <div class="card">
          <div class="image">
            <img src="@/assets/images/profit/img_card_photo.png" alt="" />
          </div>
          <span class="title">User{{ index + 1 }}</span>
          <Input
            :props-label="t('pages.profit.cost')"
            :input-type="'number'"
            v-model="user.value"
          />
          <div class="price">
            {{ t("pages.profit.profit") }}: {{ user?.result ?? "??" }}
          </div>
        </div>
      </div>
    </div>
    <Input
      :props-label="t('pages.profit.income')"
      :input-type="'number'"
      v-model="income"
    />
    <div class="profit-outputs">
      <div class="profit-submit submit-btn" @click="addPerson">
        {{ t("pages.profit.add") }}
      </div>
      <div class="profit-submit submit-btn" @click="deletePerson">
        {{ t("pages.profit.delete") }}
      </div>
      <div class="profit-submit submit-btn" @click="count">
        {{ t("button.count") }}
      </div>
    </div>
  </div>
</template>
<script>
import { ref } from "vue";
import useI18n from "@/hooks/use-i18n";
import { useAlert } from "@/hooks/use-alert";
/** components */
import Input from "@/widgets/input.vue";

export default {
  layout: "layout-backend",
  components: {
    Input,
  },
  setup() {
    const { t } = useI18n();
    const swal = useAlert();
    const inputCol = ref([
      { value: "", result: null },
      { value: "", result: null },
      // { value: "", result: null },
      // { value: "", result: null },
      // { value: "", result: null },
      // { value: "", result: null },
      // { value: "", result: null },
      // { value: "", result: null },
      // { value: "", result: null },
    ]);
    const income = ref("");
    const addPerson = () => {
      const format = { value: "", result: null };
      inputCol.value.push(format);
    };
    const deletePerson = () => {
      const deletedAry = inputCol.value.filter(
        (itme, index, ary) => index !== ary.length - 1
      );
      inputCol.value = [...deletedAry];
    };

    const count = () => {
      const data = [...inputCol.value];
      const totalCost = inputCol.value.reduce((accumulator, user) => {
        const currentValue = +user.value;
        return accumulator + currentValue;
      }, 0);
      for (const user of data) {
        const num = +user.value;
        if (typeof num !== "number") {
          swal.alert({
            title: t("pages.profit.error.title"),
            text: t("pages.profit.error.text"),
          });
          return;
        }
        // const persent = (user.value / totalCost) * 100; // * 100是換成百分比 例如50%
        const persent = user.value / totalCost;
        user.result = +income.value * persent;
      }
      // inputCol.value = data;
    };
    return {
      t,
      count,
      addPerson,
      deletePerson,
      income,
      inputCol,
    };
  },
};
</script>
