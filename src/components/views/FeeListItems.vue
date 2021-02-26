<template>
  <div id="fee-list">
    <div class="menu">
      <div class="menu__tab">Quy trình</div>
      <div class="menu__tab current--tab">Danh sách khoản thu</div>
      <div class="menu__tab">Đăng ký khoản thu</div>
      <div class="menu__tab">Danh sách miễn giảm</div>
    </div>
    <div class="option">
      <div class="option__item float--left">
        <input
          type="checkbox"
          id="displayFeeInActive"
          v-model="showFeeInactive"
        />
        <label for="displayFeeInActive"
          ><span></span>Hiển thị khoản thu ngừng theo dõi</label
        >
      </div>
      <div class="option__item float--right">
        <button
          class="m-icon-button icon-delete"
          @click="btnDeleteOnClick_n"
          :disabled="!haveRowOnSelect"
        ></button>
      </div>
      <div class="option__item float--right">
        <button class="m-second-button" @click="btnOderBy">
          Sắp lại thứ tự
        </button>
      </div>
      <div class="option__item float--right">
        <button class="m-button" @click="btnAddOnClick">Thêm mới</button>
      </div>
    </div>
    <div class="table">
      <table>
        <thead>
          <tr class="odd-row">
            <th class="selectCol">
              <input type="checkbox" id="0" v-model="selectAllRows" />
              <label for="0"><span></span></label>
            </th>
            <th v-for="col in cols" :key="col.key" :class="col.key">
              {{ col.title }}
            </th>
            <th class="optionCol"></th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(fee, index) in listFee"
            :key="index"
            v-show="fee.isActive || showFeeInactive"
            :class="{
              'odd-row': index % 2 === 1,
              'row-on-select': feeIds[fee.feeID] === true,
            }"
          >
            <td class="selectCol">
              <input
                type="checkbox"
                :id="fee.feeID"
                v-model="feeIds[fee.feeID]"
              />
              <label :for="fee.feeID"><span></span></label>
            </td>
            <td v-for="col in cols" :key="col.key" :class="col.key">
              <span v-if="col.key === 'feeName'" class="m-flex">
                <span @click="btnEditOnClick(fee.feeID)" class="fee-name">{{
                  fee[col.key]
                }}</span>
                <span
                  v-if="fee.isSystem"
                  class="icon-i"
                  title="Đây là khoản thu mặc định của hệ thống, bạn không thể xóa."
                ></span>
              </span>
              <div v-else-if="col.key === 'price'" class="m-text-right">
                {{ fee | priceRate }}
              </div>
              <div v-else-if="col.key === 'period'">
                {{ fee | periodName }}
              </div>
              <div v-else-if="col.key === 'unitname'">
                <span>Đầu</span>
                {{ fee | unitName }}
              </div>
              <div v-else-if="isBool(fee[col.key])" class="cell-center">
                <div
                  :class="{ 'icon-checkbox': true, 'icon-check': fee[col.key] }"
                ></div>
              </div>
              <span v-else-if="col.key !== 'feeName'">{{ fee[col.key] }}</span>
            </td>
            <td class="optionCol">
              <div class="m-flex">
                <div
                  class="icon icon-edit"
                  @click="btnEditOnClick(fee.feeID)"
                ></div>
                <div
                  class="icon icon-duplicate"
                  @click="btnDuplicateOnClick(fee.feeID)"
                ></div>
                <div
                  class="icon icon-delete"
                  @click="btnDeleteOnClick_1(fee.feeID)"
                ></div>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="footer">Tổng số: {{ listFee.length }} kết quả</div>
    <FeeDetail
      v-if="formDetail"
      @close="closeForm"
      :mode="formMode"
      :listFeeGroup="listFeeGroup"
      :feeId="feeIdChange"
      @loadData="loadData"
    />
    <DialogDeleteConfirm
      v-if="dialogDelete"
      @close="dialogDelete = false"
      @loadData="loadData"
      :message="messageDelete"
      :mode="formMode"
      :formItems="formItems"
      :listFeeId="listFeeIdDelete"
    />
  </div>
</template>
<script>
import FeeDetail from "./FeeDetail";
import DialogDeleteConfirm from "./DialogDeleteConfirm.vue";
import axios from "axios";

export default {
  data() {
    return {
      cols: [
        {
          key: "feeName",
          title: "Tên khoản thu",
        },
        {
          key: "feeGroupName",
          title: "Nhóm khoản thu",
        },
        {
          key: "price",
          title: "Mức thu",
        },
        {
          key: "period",
          title: "Kỳ thu",
        },
        {
          key: "unitname",
          title: "Thời điểm thu",
        },
        {
          key: "isApplyRemisson",
          title: "Áp dụng miễn giảm",
        },
        {
          key: "allowCreateInvoice",
          title: "Cho xuất hóa đơn",
        },
        {
          key: "allowCreateReceipt",
          title: "Cho xuất chứng từ",
        },
        {
          key: "isInternal",
          title: "Cho phép hoàn trả",
        },
        {
          key: "isRequire",
          title: "Khoản thu bắt buộc",
        },
        {
          key: "isActive",
          title: "Đang theo dõi",
        },
      ],
      listFee: [],
      listFeeGroup: [],
      feeIds: {},
      formDetail: false,
      dialogDelete: false,
      formMode: null,
      formItems: null,
      selectAllRows: false,
      feeIdChange: null,
      showFeeInactive: false,
      messageDelete: "",
      listFeeIdDelete: [],
      listFeeOderBy: [],
    };
  },
  components: {
    FeeDetail,
    DialogDeleteConfirm,
  },
  mounted() {
    this.loadData();
  },
  methods: {
    btnAddOnClick() {
      this.feeIdChange = null;
      this.formMode = "ADD";
      this.formDetail = true;
    },
    btnEditOnClick(feeID) {
      this.feeIdChange = feeID;
      this.formMode = "EDIT";
      this.formDetail = true;
    },
    btnDuplicateOnClick(feeId) {
      this.feeIdChange = feeId;
      this.formMode = "ADD";
      this.formDetail = true;
    },
    btnDeleteOnClick_1(feeId) {
      if (
        this.listFee.filter((fee) => {
          return fee.feeID === feeId;
        })[0].isSystem === true
      ) {
        this.formMode = "NOTIFICATION";
        this.messageDelete = "Bạn không thể xóa dữ liệu của hệ thống.";
      } else {
        this.listFeeIdDelete = [];
        this.listFeeIdDelete.push(feeId);
        this.formMode = "DELETE";
        this.messageDelete = "Bạn có chắc chắn muốn xóa khoản thu đã chọn?";
      }
      this.dialogDelete = true;
    },
    btnDeleteOnClick_n() {
      this.listFeeIdDelete = [];
      for (let feeId in this.feeIds) {
        if (this.feeIds[feeId] === true) {
          let fee_ = this.listFee.filter((fee) => {
            return fee.feeID == feeId;
          })[0];
          if (fee_ && fee_.isSystem === true) {
            this.formMode = "NOTIFICATION";
            this.messageDelete = "Bạn không thể xóa dữ liệu của hệ thống.";
            this.dialogDelete = true;
            return;
          } else if (fee_.IsActive || this.showFeeInactive) {
            this.listFeeIdDelete.push(feeId);
          }
        }
      }
      this.formMode = "DELETELISTITEM";
      this.messageDelete = "Bạn có chắc chắn muốn xóa những khoản thu đã chọn?";
      this.dialogDelete = true;
    },
    closeForm() {
      this.formMode = null;
      this.formDetail = false;
      this.dialogDelete = false;
      this.messageDelete = "";
      this.listFeeIdDelete = [];
    },
    btnOderBy() {
      axios
        .get("https://localhost:44307/api/v1/Fee/GetOderBy")
        .then((res) => {
          this.listFee = res.data;
        })
        .catch((res) => {
          this.$toaster.error(res.response.data.userMsg, {timeout: 3000})
        });
      axios
        .get("https://localhost:44307/api/v1/FeeGroup")
        .then((res) => {
          this.listFeeGroup = res.data;
        })
        .catch((res) => {
          this.$toaster.error(res.response.data.userMsg, {timeout: 3000})
        });
    },
    loadData() {
      axios
        .get("https://localhost:44307/api/v1/Fee")
        .then((res) => {
          this.listFee = res.data;
        })
        .catch((res) => {
          this.$toaster.error(res.response.data.userMsg, {timeout: 3000})
        });
      axios
        .get("https://localhost:44307/api/v1/FeeGroup")
        .then((res) => {
          this.listFeeGroup = res.data;
        })
        .catch((res) => {
          this.$toaster.error(res.response.data.userMsg, {timeout: 3000})
        });
    },
    isBool(val) {
      if (typeof val === typeof true) return true;
      return false;
    },
  },
  computed: {
    haveRowOnSelect() {
      for (let feeId in this.feeIds) {
        if (this.feeIds[feeId] === true) {
          let fee_ = this.listFee.filter((fee) => {
            return fee.feeID == feeId;
          })[0];
          if (fee_.IsActive || this.showFeeInactive) {
            return true;
          }
        }
      }
      return false;
    },
  },
  filters: {
    priceRate(fee) {
      var result = "";
      var price = fee.price;
      var i = 0;
      while (price > 0) {
        if (i !== 0 && i % 3 == 0) {
          result = "." + result;
        }
        result = (price % 10) + result;
        price = Math.floor(price / 10);
        ++i;
      }
      switch (fee.unit) {
        case 1:
          result += "đ/ngày";
          break;
        case 2:
          result += "đ/tuần";
          break;
        case 3:
          result += "đ/tháng";
          break;
        case 4:
          result += "đ/học kỳ";
          break;
        case 5:
          result += "đ/năm";
          break;
        default:
          break;
      }
      return result;
    },
    periodName(fee) {
      switch (fee.period) {
        case 1:
          return "Tháng";
        case 2:
          return "Quý";
        case 3:
          return "Học kỳ";
        case 4:
          return "Năm học";
        default:
          return "";
      }
    },
    unitName(fee) {
      switch (fee.unit) {
        case 1:
          return "ngày";
        case 2:
          return "tuần";
        case 3:
          return "tháng";
        case 4:
          return "học kì";
        case 5:
          return "năm học";
        default:
          return "";
      }
    },
  },
};
</script>
<style scoped>
@import "../../styles/views/FeeListItems.css";
</style>