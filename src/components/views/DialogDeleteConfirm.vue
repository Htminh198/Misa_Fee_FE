<template>
  <div id="dialog-delete">
    <div class="modal"></div>
    <div class="dialog">
      <div class="dialog__header">
        <div class="title">Thông báo</div>
        <div class="icon icon-x" @click="close"></div>
      </div>
      <div class="dialog__content">{{ message }}</div>
      <div class="dialog__footer">
        <div class="footer__item float--left"></div>
        <div
          class="footer__item float--right"
          v-if="mode === 'NOTIFICATION' || mode === null"
        >
          <button class="m-button" @click="close">Đồng ý</button>
        </div>
        <div class="footer__item float--right" v-if="mode === 'DELETELISTITEM'">
          <button class="m-button" @click="deleteListFee">Xóa</button>
        </div>
        <div class="footer__item float--right" v-if="mode === 'DELETELISTITEM'">
          <button class="m-second-button" @click="close">Không</button>
        </div>
        <div class="footer__item float--right" v-if="mode === 'DELETE'">
          <button class="m-button" @click="deleteFee">Xóa</button>
        </div>
        <div class="footer__item float--right" v-if="mode === 'DELETE'">
          <button class="m-second-button" @click="close">Không</button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";

export default {
  props: {
    message: String,
    listFeeId: Array,
    mode: String,
    formItems: String,
  },
  methods: {
    close() {
      this.$emit("close");
    },
    async deleteListFee() {
      for (let i in this.listFeeId) {
        await axios
          .delete(`https://localhost:44307/api/v1/Fee/${this.listFeeId[i]}`)
          .then()
          .catch((res) => {
            this.$toaster.error(res.response.data.userMsg, {timeout: 3000})
          });
      }
      this.$emit("close");
      this.$emit("loadData");
      this.$toaster.error("Xóa thành công", {timeout: 3000})
      location.reload();
    },
    async deleteFee() {
      for (let i in this.listFeeId) {
        await axios
          .delete(`https://localhost:44307/api/v1/Fee/${this.listFeeId[i]}`)
          .then((res) => {
            this.$toaster.success(res.data, {timeout: 3000})
          })
          .catch((res) => {
            this.$toaster.error(res.response.data.userMsg, {timeout: 3000})
          });
      }
      this.$emit("close");
      this.$emit("loadData");
    },
  },
};
</script>
<style scoped>
@import "../../styles/views/DialogDeleteConfirm.css";
</style>