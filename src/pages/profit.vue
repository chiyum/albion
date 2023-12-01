<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div class="profit">
    <Input :props-label="'Name'" :model-value="value" />
  </div>
</template>
<script>
import { onMounted, reactive, ref } from "vue";
import { useI18n } from "@/hooks/use-i18n";
import { useRouter } from "vue-router";
import { usePopup } from "@/hooks/use-popup";
import { useAlert } from "@/hooks/use-alert";
import Input from "@/widgets/input.vue";

export default {
  layout: "layout-backend",
  components: {
    Input,
  },
  setup() {
    // import storage from "store2";
    const value = ref("");
    const popup = usePopup();
    const { t, locale, setPrefix, change } = useI18n();
    setPrefix({
      $current: "pages.demo",
    });
    const router = useRouter();
    const swal = useAlert();
    const lang = reactive({
      current: locale.value || "zh-tw",
    });
    const popupModal = async () => {
      await popup.modal({
        title: "popup",
        text: "this a popup",
        // props: {
        //   title: t("pages.home.notify.vip.title"),
        //   text: "VIP 1",
        // },
      });
    };
    const toHome = () => {
      router.push("/home");
    };
    const swalModal = () => {
      swal.alert({
        title: t("$current.modal.swal.title"),
        text: t("$current.modal.swal"),
      });
    };
    const changeLang = () => {
      const changed = lang.current === "zh-tw" ? "en" : "zh-tw";
      lang.current = changed;
      change(changed);
      // lang.current = changed;
      // storage.set("locale", changed);
    };
    onMounted(() => {});
    return {
      t,
      lang,
      value,
      toHome,
      swalModal,
      changeLang,
      popupModal,
    };
  },
};
</script>
<style lang="scss">
@import "@/assets/scss/home.scss";
</style>
