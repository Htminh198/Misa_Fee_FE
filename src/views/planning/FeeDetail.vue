<template>
  <div id="fee-detail">
    <div class="modal"></div>
    <div class="dialog">
      <div class="dialog__header">
        <div class="title">
          {{
            mode === "ADD"
              ? "Thêm khoản thu"
              : mode === "EDIT"
              ? "Sửa khoản thu"
              : "..."
          }}
        </div>
        <div class="icon icon-x" @click="close"></div>
      </div>
      <div class="dialog__content">
        <div class="content__left">
          <div class="row">
            <div class="text">Tên khoản thu <span class="m-red">*</span></div>
            <div class="input">
              <input
                type="text"
                v-focus
                size="100"
                @blur="validateData('feeName')"
                :class="{
                  'm-input': true,
                  'error-required': !validate.feeName.Status,
                }"
                :title="
                  validate.feeName.Status
                    ? null
                    : 'Bạn phải nhập thông tin này!'
                "
                v-model="fee.feeName"
              />
            </div>
          </div>
          <div class="row">
            <div class="text">Thuộc nhóm khoản thu</div>
            <div class="input">
              <select name="feeGroup" v-model="fee.feeGroupID" class="m-select">
                <option
                  v-for="(feeGroup, index) in listFeeGroup"
                  :key="index"
                  :value="feeGroup.feeGroupID"
                >
                  {{ feeGroup.feeGroupName }}
                </option>
              </select>
            </div>
          </div>
          <div class="row m-flex">
            <div class="m-flex-3">
              <div class="text">Mức thu <span class="m-red">*</span></div>
              <div class="input">
                <input
                  type="number"
                  v-model="fee.price"
                  @blur="validateData('price')"
                  :class="{
                    'm-input': true,
                    'error-required': !validate.price.Status,
                    'm-text-right': true,
                  }"
                  :title="
                    validate.price.Status
                      ? null
                      : 'Bạn phải nhập thông tin này!'
                  "
                />
                <label>đ/</label>
              </div>
            </div>
            <div class="m-flex-1">
              <div class="text">Đơn vị tính <span class="m-red">*</span></div>
              <div class="input">
                <select
                  name="unit"
                  v-model="fee.unit"
                  @blur="validateData('Unit')"
                  :class="{
                    'm-select': true,
                    'error-required': !validate.unit.Status,
                  }"
                  :title="
                    validate.unit.Status ? null : 'Bạn phải nhập thông tin này!'
                  "
                >
                  <option value="1">Ngày</option>
                  <option value="2">Tuần</option>
                  <option value="3">Tháng</option>
                  <option value="4">Học kỳ</option>
                  <option value="5">Năm</option>
                </select>
              </div>
            </div>
          </div>
          <div class="row">
            <div class="text">Phạm vi thu <span class="m-red">*</span></div>
            <div class="input">
              <select
                name="applyObject"
                v-model="fee.applyObject"
                @blur="validateData('ApplyObject')"
                :class="{
                  'm-select': true,
                  'error-required': !validate.applyObject.Status,
                }"
                :title="
                  validate.applyObject.Status
                    ? null
                    : 'Bạn phải nhập thông tin này!'
                "
              >
                <option value="Cá nhân">Cá nhân</option>
                <option value="Lớp">Lớp</option>
                <option value="Khối">Khối</option>
                <option value="Toàn trường">Toàn trường</option>
              </select>
            </div>
          </div>
          <div class="row">
            <div class="text">Tính chất</div>
            <div class="input">
              <select name="property" class="m-select" v-model="fee.property">
                <option value="0">Thu theo thỏa thuận</option>
                <option value="1">Chi phí phát sinh</option>
              </select>
            </div>
          </div>
          <div class="row">
            <div class="text">Kỳ thu <span class="m-red">*</span></div>
            <div class="input">
              <div class="m-flex m-flex-1">
                <input
                  type="radio"
                  name="period"
                  v-model="fee.period"
                  value="1"
                  id="1-period"
                />
                <label for="1-period"><span></span>Tháng</label>
              </div>
              <div class="m-flex m-flex-1">
                <input
                  type="radio"
                  name="period"
                  v-model="fee.period"
                  value="2"
                  id="2-period"
                />
                <label for="2-period"><span></span>Quý</label>
              </div>
              <div class="m-flex m-flex-1">
                <input
                  type="radio"
                  name="period"
                  v-model="fee.period"
                  value="3"
                  id="3-period"
                />
                <label for="3-period"><span></span>Học kỳ</label>
              </div>
              <div class="m-flex m-flex-1">
                <input
                  type="radio"
                  name="period"
                  v-model="fee.period"
                  value="4"
                  id="4-period"
                />
                <label for="4-period"><span></span>Năm học</label>
              </div>
            </div>
          </div>
        </div>
        <div class="content__right">
          <div class="row m-flex">
            <div class="m-flex-1">
              <input
                type="checkbox"
                id="IsApplyRemisson"
                v-model="fee.isApplyRemisson"
              />
              <label for="IsApplyRemisson"
                ><span></span>Áp dụng miễn giảm</label
              >
            </div>
            <div class="m-flex-1">
              <input
                type="checkbox"
                id="AllowCreateReceipt"
                v-model="fee.allowCreateReceipt"
              />
              <label for="AllowCreateReceipt"
                ><span></span>Cho phép xuất chứng từ</label
              >
            </div>
          </div>
          <div class="row m-flex">
            <div class="m-flex-1">
              <input type="checkbox" id="IsRequire" v-model="fee.isRequire" />
              <label for="IsRequire"><span></span>Khoản thu bắt buộc</label>
            </div>
            <div class="m-flex-1">
              <input type="checkbox" id="IsInternal" v-model="fee.isInternal" />
              <label for="IsInternal"><span></span>Cho phép hoàn trả</label>
            </div>
          </div>
          <div class="row m-flex">
            <div class="m-flex-1">
              <input
                type="checkbox"
                id="AllowCreateInvoice"
                v-model="fee.allowCreateInvoice"
              />
              <label for="AllowCreateInvoice"
                ><span></span>Cho phép xuất hóa đơn</label
              >
            </div>
            <div class="m-flex-1">
              <input type="checkbox" id="IsSystem" v-model="fee.isSystem" />
              <label for="IsSystem"><span></span>Thu nội bộ</label>
            </div>
          </div>
          <div class="row m-flex">
            <div class="m-flex-1">
              <input type="checkbox" />
              <label>Phân loại đăng ký</label>
            </div>
          </div>
        </div>
      </div>
      <div class="dialog__footer">
        <div class="footer__item float--left" v-if="mode === 'EDIT'">
          <input type="checkbox" id="IsActive" v-model="fee.isActive" />
          <label for="IsActive"
            ><span class="convert"></span>Ngừng theo dõi</label
          >
        </div>
        <div class="footer__item float--right">
          <button class="m-button" @click="saveOnClick(true)">Lưu</button>
        </div>
        <div class="footer__item float--right" v-if="mode === 'ADD'">
          <button class="m-button" @click="saveOnClick(false)">
            Lưu và thêm mới
          </button>
        </div>
        <div class="footer__item float--right">
          <button class="m-second-button" @click="close()">Đóng</button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";

export default {
  data() {
    return {
      fee: {
        feeName: "",
        feeGroupID: null,
        price: null,
        unit: 2,
        applyObject: 3,
        property: null,
        period: "1",
        isApplyRemisson: false,
        isRequire: false,
        allowCreateInvoice: true,
        allowCreateReceipt: true,
        isActive: true,
        isInternal: false,
        isSystem: false,
      },
      validate: {
        feeName: {
          Status: true,
          Require: true,
        },
        price: {
          Status: true,
          Require: true,
        },
        unit: {
          Status: true,
          Require: true,
        },
        applyObject: {
          Status: true,
          Require: true,
        },
        period: {
          Status: true,
          Require: true,
        },
        isApplyRemisson: {
          Status: true,
          Require: true,
        },
        isRequire: {
          Status: true,
          Require: true,
        },
        allowCreateInvoice: {
          Status: true,
          Require: true,
        },
        allowCreateReceipt: {
          Status: true,
          Require: true,
        },
        isActive: {
          Status: true,
          Require: true,
        },
        isInternal: {
          Status: true,
          Require: true,
        },
        isSystem: {
          Status: true,
          Require: true,
        },
      },
    };
  },
  props: {
    mode: String,
    listFeeGroup: Array,
    feeId: Number,
  },
  methods: {
    close() {
      this.$emit("close");
    },
    loadData() {
      this.$emit("loadData");
    },
    validateData(key) {
      if (
        this.validate[key].Require == true &&
        (this.fee[key] === null || this.fee[key] === "")
      ) {
        this.validate[key].Status = false;
      } else {
        this.validate[key].Status = true;
      }
    },
    saveOnClick(close) {
      var flag = true;
      for (var key in this.validate) {
        this.validateData(key);
        if (this.validate[key].Status === false) {
          flag = false;
          break;
        }
      }
      if (flag && this.mode === "ADD") {
        axios
          .post("https://localhost:44307/api/v1/Fee", this.fee)
          .then((res) => {
            alert(res.data);
            this.loadData();
            this.setDefaul();
            if (close) this.close();
          })
          .catch((res) => {
            alert(res.response.data.userMsg);
          });
      } else if (flag && this.mode === "EDIT") {
        axios
          .put("https://localhost:44307/api/v1/Fee", {
            feeID: this.feeId,
            feeName: this.fee.feeName,
            feeGroupID: this.fee.feeGroupID,
            price: this.fee.price,
            unit: this.fee.unit,
            applyObject: this.fee.applyObject,
            property: this.fee.property,
            period: this.fee.period,
            isApplyRemisson: this.fee.isApplyRemisson,
            isRequire: this.fee.isRequire,
            allowCreateInvoice: this.fee.allowCreateInvoice,
            allowCreateReceipt: this.fee.allowCreateReceipt,
            isActive: this.fee.isActive,
            isInternal: this.fee.isInternal,
            isSystem: this.fee.isSystem,
          })
          .then((res) => {
            alert(res.data);
            this.loadData();
            location.reload();
            this.close();
          })
          .catch((res) => {
            alert(res.response.data.userMsg);
          });
      }
    },
    setDefaul(){
      console.log(1)
        this.fee.feeName= "",
        this.fee.feeGroupID= null,
        this.fee.price= null,
        this.fee.unit= 2,
        this.fee.applyObject= 3,
        this.property= null,
        this.fee.period= "1",
        this.fee.isApplyRemisson= false,
        this.fee.isRequire= false,
        this.fee.allowCreateInvoice= true,
        this.fee.allowCreateReceipt= true,
        this.fee.isActive= true,
        this.fee.isInternal= false,
        this.fee.isSystem= false
    }
  },
  directives: {
    focus: {
      inserted: function (el) {
        el.focus();
      },
    },
  },
  mounted() {
    if (this.feeId !== null) {
      axios
        .get("https://localhost:44307/api/v1/Fee/" + this.feeId)
        .then((res) => {
          this.fee = res.data;
        })
        .catch((res) => {
          alert(res.response.data.userMsg);
        });
    }
  },
};
</script>
<style scoped>
.modal {
  position: fixed;
  width: 100vw;
  height: 100vh;
  top: 0;
  left: 0;
  background-color: #cccccc;
  opacity: 0.6;
  z-index: 3;
}

.dialog {
  position: fixed;
  z-index: 3;
  width: calc(1000px - 24px - 24px);
  height: 600px;
  top: calc((100vh - 600px) / 2);
  left: calc((100vw - 1000px) / 2);
  background-color: #fff;
  border-radius: 4px;
  padding-left: 24px;
  padding-right: 24px;
  overflow: hidden;
}

.dialog .dialog__header {
  display: flex;
  align-items: center;
  width: 100%;
  height: 70px;
  overflow: hidden;
}

.dialog .dialog__header .title {
  font-family: "OpenSans-Bold";
  font-size: 20px;
}

.dialog .dialog__header .icon {
  position: absolute;
  top: 12px;
  right: 12px;
  width: 16px;
  height: 16px;
  border-radius: 50%;
  cursor: pointer;
}

.dialog .dialog__content {
  display: flex;
  width: 100%;
  height: calc(100% - 70px - 70px);
}

.dialog .dialog__content .content__left {
  flex: 1;
  padding: 12px 24px 12px 0;
  height: calc(100% - 24px);
  border-right: 1px solid #cccccc;
}

.dialog .dialog__content .content__left .row {
  width: 100%;
  height: 66px;
}

.dialog .dialog__content .content__left .row .text {
  line-height: 32px;
}

.dialog .dialog__content .content__left .row .input {
  display: flex;
}

.dialog .dialog__content .content__left .row .input label {
  display: flex;
  align-items: center;
  padding: 0 4px 0 4px;
}

.dialog .dialog__content .content__right {
  flex: 1;
  height: calc(100% - 24px);
  padding: 12px 0 12px 24px;
}

.dialog .dialog__content .content__right .row {
  width: 100%;
  height: 40px;
  overflow: hidden;
}

.dialog .dialog__content .content__right .row > div {
  display: flex;
  align-items: center;
}

.dialog .dialog__footer {
  width: 100%;
  height: 70px;
}

.dialog .dialog__footer .footer__item {
  height: 100%;
  display: flex;
  align-items: center;
}

.dialog .dialog__footer .footer__item.float--right {
  margin-left: 8px;
}
.error-required {
  border: 1px solid red;
}
</style>