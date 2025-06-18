<template>
  <div class="home-page">
    <Sidebar class="sidebar" @menu-click="changePage($event)" />
    <main class="main">
      <Dashboard v-if="activePage == 'dashboard'" />
      <Managers v-if="activePage == 'managers'" />
      <Backery v-if="activePage == 'workers'" />
      <Settings v-if="activePage == 'settings'" />
      <TypeOfBreadVue v-if="activePage == 'typeOfBread'" />
      <DeliveryVue v-if="activePage == 'delivery'" />
      <MagazineVue v-if="activePage == 'magazine'" />
      <DebtVue v-if="activePage == 'debt'" />
      <WarehouseVue v-if="activePage == 'warehouse'" />
      <SellerShopVue v-if="activePage == 'sellerShop'" />
      <SellerNonVue v-if="activePage == 'sellerNon'" />
      <SellerDebtVue v-if="activePage == 'sellerDebt'" />
      <DeliverySellerVue v-if="activePage == 'deliverySeller'" />
      <DeliveryDebtVue v-if="activePage === 'deliveryDebt'" />
      <DeliveryHistoryVue v-if="activePage === 'deliveryHistory'" />
      <SellerDashVue v-if="activePage === 'sellerDash'" />
      <HomeDeliveryVue v-if="activePage === 'homeDelivery'" />
      <ManagerdashboardVue v-if="activePage === 'Managerdashboard'" />
      <SellerHistoryVue v-if="activePage === 'sellerHistory'" />
      <Returned v-if="activePage === 'returned'" />
      <Sale v-if="activePage === 'sale'" />
      <InvalidPro v-if="activePage === 'invalidPro'" />
      <!-- <ErrorPage v-else/> -->
    </main>
  </div>
</template>

<script>
import Sidebar from "@/components/Template/Sidebar.vue";
import Dashboard from "@/Pages/Dashboard/dashboard.vue";
import Backery from "@/Pages/Backery/backery.vue";
import Settings from "@/Pages/Settings/settings.vue";
import TypeOfBreadVue from "@/Pages/typeOfBread/typeOfBread.vue";
import DeliveryVue from "@/Pages/Delivery/delivery.vue";
import MagazineVue from "@/Pages/Magazine/magazine.vue";
import DebtVue from "@/Pages/Debt/debt.vue";
import WarehouseVue from "@/Pages/WareHouse/warehouse.vue";
import SellerShopVue from "@/Pages/SellerShop/sellerShop.vue";
import SellerNonVue from "@/Pages/sellerNon/sellerNon.vue";
import Managers from "@/Pages/Managers/managers.vue";
import SellerDebtVue from "@/Pages/SellerDebt/sellerDebt.vue";
import DeliverySellerVue from "@/Pages/deliverySeller/deliverySeller.vue";
import DeliveryDebtVue from "@/Pages/deliveryDebt/deliveryDebt.vue";
import DeliveryHistoryVue from "@/Pages/deliveryHistory/deliveryHistory.vue";
import SellerDashVue from "@/Pages/sellerDash/sellerDash.vue";
import HomeDeliveryVue from "@/Pages/homeDelivery/homeDelivery.vue";
import ManagerdashboardVue from "@/Pages/Managerdashboard/managerdashboard.vue";
import SellerHistoryVue from "@/Pages/sellerHistory/sellerHistory.vue";
import Returned from "@/Pages/ReturnedPage/returned.vue";
import Sale from "@/Pages/Sale/sale.vue";
import InvalidPro from "@/Pages/InvalidPro/InvalidPro.vue";
export default {
  name: "HomePage",
  components: {
    Sidebar,
    Dashboard,
    Managers,
    Backery,
    Settings,
    TypeOfBreadVue,
    DeliveryVue,
    MagazineVue,
    DebtVue,
    WarehouseVue,
    SellerShopVue,
    SellerNonVue,
    SellerDebtVue,
    DeliverySellerVue,
    DeliveryDebtVue,
    DeliveryHistoryVue,
    SellerDashVue,
    HomeDeliveryVue,
    ManagerdashboardVue,
    SellerHistoryVue,
    Returned,
    Sale,
    InvalidPro,
  },
  data() {
    return {
      activePage: "",
    };
  },
  methods: {
    changePage(page) {
      this.activePage = page;
    },
  },
};
</script>

<style></style>