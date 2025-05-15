<template>
  <div class="page">
    <div class="page-top d-flex a-center j-between">
      <h3>Qaytarilganlar</h3>
      <button
        class="create-button"
        @click="openModal = true"
        v-if="role === 'delivery'"
      >
        Qaytarish
      </button>
    </div>
    <div class="scroll page-bottom p-24">
      <div class="table">
        <div class="table-header">
          <div class="row">
            <div class="cell">№</div>
            <div class="cell">Nomi</div>
            <div class="cell">Soni</div>
            <div class="cell">Narxi</div>
            <div class="cell">Umumiy hisob</div>
            <!-- <div class="cell">Tavsifi</div> -->
            <div class="cell">Sana</div>
            <div class="cell" v-if="role == 'manager'"></div>
          </div>
        </div>
        <div class="table-body">
          <div class="row" v-for="(data, index) in returnedPro" :key="index">
            <div class="cell">{{ index + 1 }}</div>
            <div class="cell">{{ data.title || "" }}</div>
            <div class="cell">
              {{ formatPrice(data?.totalQuantity) || 0 }}
            </div>
            <div class="cell">{{ formatPrice(data?.price) || 0 }} so`m</div>
            <div class="cell">
              {{ formatPrice(data?.totalPrice) || 0 }} so`m
            </div>
            <div class="cell">
              {{ formatDate(new Date(data.createdAt)) }}
            </div>
            <!-- <div class="cell">{{ data?.order.description || "" }}</div> -->
            <!-- <div class="cell">
              {{ data?.order.totalQuantity || 0 }}
            </div> -->
            <!-- <div class="cell">
              {{ formatDate(new Date(data.order.createdAt)) }}
            </div> -->
            <div
              class="cell d-flex a-center j-end gap12"
              v-if="role == 'manager'"
            >
              <Icons
                name="delivery"
                title="yetkazuvchi"
                class="icon info setting"
                @click="openModal2(data)"
              />

              <Icons
                name="invalidPro"
                class="icon info setting"
                @click="giveInvalidPro(data)"
                title="yaroqsiz"
              />
              <Icons
                name="firma"
                class="icon info setting"
                @click="giveWarehouse(data)"
                title="omborxona"
              />
            </div>
          </div>
        </div>
        <p class="text16 d-flex j-center p-24" v-if="!returnedPro.length">
          Qaytarilgan nonlar hozir mavjud emas
        </p>
      </div>
    </div>
  </div>
  <ReturnedModalVue
    v-if="openModal"
    @close="(openModal = false), getAllReturned()"
    @status="handleStatus($event)"
  />
  <ToastiffVue :toastOptions="toastOptions" />
  <Returned2ModalVue
    v-if="open2Modal"
    @close="(open2Modal = true), getAllReturned()"
    :datas="datas"
    @status="handleStatus($event)"
  />
</template>


<script>
import api from "@/Utils/axios";
import ReturnedModalVue from "./returnedModal.vue";
import ToastiffVue from "@/Utils/Toastiff.vue";
import Icons from "@/components/Template/Icons.vue";
import Returned2ModalVue from "./returnedModal2.vue";
export default {
  components: { ReturnedModalVue, ToastiffVue, Icons, Returned2ModalVue },
  data() {
    return {
      openModal: false,
      role: JSON.parse(localStorage.getItem("user"))?.role || "",
      returnedPro: [],
      toastOptions: {
        open: false,
        text: "",
        style: { background: "#4CAF50" },
      },
      open2Modal: false,
      datas: [],
    };
  },
  methods: {
    openModal2(data) {
      this.datas = data;
      this.open2Modal = true;
    },
    formatDate(date) {
      const day = String(date.getDate()).padStart(2, "0");
      const month = String(date.getMonth() + 1).padStart(2, "0");
      const year = date.getFullYear();

      return `${day}.${month}.${year}`;
    },
    formatPrice(price) {
      return new Intl.NumberFormat("ru-RU").format(price);
    },
    handleStatus(data) {
      this.toastOptions = {
        open: true,
        text: data?.message,
        type: data?.status,
      };
    },
    getAllReturned() {
      api
        .get("/api/returnedPros")
        .then(({ status, data }) => {
          if (status === 200) {
            this.returnedPro = data.returnedPro.reduce((acc, item) => {
              item.order.typeOfBreadIds.forEach((breadItem) => {
                const _id = breadItem.breadId._id;

                if (!acc[_id]) {
                  acc[_id] = {
                    title: breadItem.breadId.title,
                    totalQuantity: 0,
                    totalQuantity2: 0,
                    totalPrice: 0,
                    createdAt: item.order.createdAt,
                    returnedId: item._id,
                    orderId: item.order,
                    price: 0,
                  };
                }

                acc[_id].totalQuantity += breadItem.quantity;
                acc[_id].totalQuantity2 += item.order.totalQuantity;
                acc[_id].totalPrice += (item.order.pricetype === 'tan' ? breadItem.breadId?.price : item.order.pricetype === 'dokon' ? breadItem.breadId?.price2 : item.order.pricetype === 'toyxona' ? breadItem.breadId?.price3 : breadItem.price) * breadItem.quantity;
                acc[_id].price = (item.order.pricetype === 'tan' ? breadItem.breadId?.price : item.order.pricetype === 'dokon' ? breadItem.breadId?.price2 : item.order.pricetype === 'toyxona' ? breadItem.breadId?.price3 : breadItem.price)
              });

              return acc;
            }, {});
            this.returnedPro = Object.values(this.returnedPro);
          }
        })
        .catch((error) => {
          console.error(error);
        });
    },
    deleteReturned(id) {
      api
        .delete("/api/returnedPro/" + id)
        .then((status) => {
          if (status === 200) {
          } else {
          }
        })
        .catch((error) => {
          console.error(error);
        });
    },
    giveInvalidPro(data) {
      api
        .post("/api/InvalidPro", { ReturnedModel: data.returnedId })
        .then(({ status }) => {
          if (status === 201) {
            this.toastOptions = {
              open: true,
              type: "success",
              text: "Non yarqosiz omborga tushdi",
            };
            this.deleteReturned(data.returnedId);
            this.getAllReturned();
          } else {
            this.toastOptions = {
              open: true,
              type: "error",
              text: "Non yarqosiz omborga tushishida Xatolik yuz berdi",
            };
          }
        })
        .catch((error) => {
          console.error(error);
          this.toastOptions = {
            open: true,
            type: "error",
            text:
              error.response.data.message ||
              "Non yarqosiz omborga tushishida Xatolik yuz berdi",
          };
        });
    },
    giveWarehouse(data) {
      api
        .post("/api/manager's/warehouse", {
          sellerId: "seller",
          totalQuantity: data.totalQuantity,
          totalQopQuantity: 1,
          bread: data.orderId.typeOfBreadIds[0].breadId._id,
        })
        .then(({ status }) => {
          if (status === 201) {
            this.toastOptions = {
              open: true,
              type: "success",
              text: "Non omborga tushdi",
            };
            this.deleteReturned(data.returnedId);
            this.getAllReturned();
          } else {
            this.toastOptions = {
              open: true,
              type: "error",
              text: "Non omborga tushishida Xatolik yuz berdi",
            };
          }
        })
        .catch((error) => {
          console.error(error);
          this.toastOptions = {
            open: true,
            type: "error",
            text:
              error.response.data.message ||
              "Non yarqosiz omborga tushishida Xatolik yuz berdi",
          };
        });
    },
  },
  mounted() {
    this.getAllReturned();
  },
};
</script>

<style>
</style>