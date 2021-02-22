<template>
  <div :class="{ isHide: isHide }" class="dialog-alert">
    <div class="dialog__background"></div>
    <div class="dialog__content">
      <div class="dialog__header">
        <p style="color: dark">Thông báo</p>
      </div>
      <div class="dialog__info">
        <div class="dialog__text">
          Bạn có muốn xóa khoản thu đã chọn không ?
        </div>
      </div>
      <div class="dialog__footer">
        <button
          ref="firstButton"
          type="button"
          autofocus
          class="m-btn btn-close-dialog m-button"
          @click="confirmDelete()"
        >
          Có
        </button>
        <button
          type="button"
          autofocus
          class="m-btn btn-close-dialog m-button"
          @click="cancelDelete()"
        >
          Không
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  props: {
    isHide: {
      type: Boolean,
      default: true,
    },
    feeId: null,
    loadData: Function
  },
  methods: {
    cancelDelete() {
      this.$emit("closeDialogConfirm", true);
    },
    confirmDelete() {
      axios
        .delete(`https://localhost:44307/api/v1/Fee/${this.feeId}`).then(this.loadData)
        this.cancelDelete()
        alert("Xóa thành công")
    },
  },
};
</script>

<style scoped>
.dialog__background {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.4);
  z-index: 1000;
}

.dialog__content {
  position: fixed;
  width: 400px;
  transform: translate(-50%, -50%);
  top: 50%;
  left: 50%;
  border: 4px solid #ffffff;
  border-radius: 4px;
  z-index: 1000;
  background-color: #fff;
}

.dialog__header {
  font-family: "OpenSans-Bold";
  font-size: 16px;
  height: 32px;
  background: #ffffff;
  color: #000;
  display: flex;
  align-items: center;
  padding-left: 8px;
  margin: 4px;
}
.dialog__footer {
  float: right;
  margin-bottom: 8px;
  margin-right: 16px;
  margin-top: 16px;
  display: flex;
  align-items: center;
  margin-right: 12px;
  margin-bottom: 12px;
}

.btn-close-dialog {
  width: 80px;
  display: flex;
  justify-content: center;
}

.btn-close-dialog + .btn-close-dialog {
  margin-left: 8px;
}

.dialog__info {
  display: flex;
  align-items: center;
}

.dialog__icon {
  width: 64px;
  height: 32px;
  margin-top: 16px;
  margin-left: 16px;
}

.icon__large {
  width: 32px;
  height: 32px;
  background-image: url("https://cdn2.cukcuk.vn/QLNH/resources/Image/icon-question.png");
}

.dialog__text {
  text-align: center;
  margin-top: 16px;
  flex: 1;
  margin-right: 16px;
  height: 100%;
  margin: 16px 0 16px 0;
}

.isHide {
  display: none;
}
</style>
