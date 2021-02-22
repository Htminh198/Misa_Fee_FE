<template>
  <div id="fee-list">
    <div class="menu">
      <div class="menu__tab">Quy trình</div>
      <div class="menu__tab current--tab">Danh sách khoản thu</div>
      <div class="menu__tab">Đăng ký khoản thu</div>
      <div class="menu__tab">danh sách miễn giảm</div>
    </div>
    <div class="option">
      <div class="option__item float--left">
        <input type="checkbox" />
        <label>Hiển thị khoản thu ngừng theo dõi</label>
      </div>
      <div class="option__item float--right">
        <button class="m-icon-button icon-delete"></button>
      </div>
      <div class="option__item float--right">
        <button class="m-second-button">Sắp lại thứ tự</button>
      </div>
      <div class="option__item float--right">
        <button class="m-button" @click="btnAddOnClick">Thêm mới</button>
      </div>
    </div>
    <div class="table">
      <table>
        <thead>
          <tr class="odd-row">
            <th>
              <div class="m-flex-1">
              <input
                type="checkbox"
                id="IsApplyRemisson"
                
              />
              <label for="IsApplyRemisson"
                ><span></span></label
              >
            </div>
            </th>
            <th v-for="col in cols" :key="col.key">{{ col.title }}</th>
            <th></th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(fee, index) in listFee"
            :key="index"
            :class="{ 'odd-row': index % 2 === 1 }"
          >
            <td style="text-align: center">
              <div class="m-flex-1">
              <input
                type="checkbox"
                id="IsApplyRemisson"
                
              />
              <label for="IsApplyRemisson"
                ><span></span></label
              >
            </div>
            </td>
            <td style="text-align: center; display: none">{{ fee.feeID }}</td>
            <!-- <td style="text-align: center" v-for="col in cols" :key="col.key">{{fee[col.key]}}</td> -->
            <td style="text-align: center"><div v-on:click="btnEditOnClick(fee.feeID)">{{ fee.feeName }}</div></td>
            <td style="text-align: center">{{ fee.feeGroupName }}</td>
            <td style="text-align: center">{{ fee.price }}</td>
            <td style="text-align: center">{{ fee.periodName }}</td>
            <td style="text-align: center">{{ fee.applyObjectName }}</td>
            <td style="text-align: center">
              <div
                class="td-checktrue-false"
                v-if="fee.isApplyRemisson === true"
              >
                <div class="icon-checktrue-false"></div>
              </div>
            </td>
            <td style="text-align: center">
              <div
                class="td-checktrue-false"
                v-if="fee.allowCreateInvoice === true"
              >
                <div class="icon-checktrue-false"></div>
              </div>
            </td>
            <td style="text-align: center">
              <div
                class="td-checktrue-false"
                v-if="fee.allowCreateReceipt === true"
              >
                <div class="icon-checktrue-false"></div>
              </div>
            </td>
            <td style="text-align: center">
              <div class="td-checktrue-false" v-if="fee.isInternal === true">
                <div class="icon-checktrue-false"></div>
              </div>
            </td>
            <td style="text-align: center">
              <div class="td-checktrue-false" v-if="fee.isRequire === true">
                <div class="icon-checktrue-false"></div>
              </div>
            </td>
            <td style="text-align: center">
              <div class="td-checktrue-false" v-if="fee.isSystem === true">
                <div class="icon-checktrue-false"></div>
              </div>
            </td>
            <td style="text-align: center">
              <div class="btn-group">
                <div
                  v-on:click="btnEditOnClick(fee.feeID)"
                  class="btn-edit"
                ></div>
                <div class="btn-dalicate"></div>
                <div
                  v-on:click="btnDeleteOnClick(fee.feeID)"
                  class="btn-delete"
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
      :loadData="loadData()"

      :listFeeGroup="listFeeGroup"
    />

    <DialogConfirm
      ref="dialogConfirm"
      :isHide="isHideDialogConfirm"
      :feeId="feeId"
      :loadData="loadData()"
      @closeDialogConfirm="isHideDialogConfirm = $event"
      @loadData="employees = $event"
    />
  </div>
</template>
<script>
import FeeDetail from "./FeeDetail";
import DialogConfirm from "./DialogConfirm";
import axios from "axios";

export default {
  data() {
    return {
      isHideDialogConfirm: true,
      feeId: "",
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
          key: "applyObject",
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
          key: "isSystem",
          title: "Đang theo dõi",
        },
      ],
      listFee: [],
      listFeeGroup: [],
      formDetail: false,
      formMode: null,
      feeByID: []
    };
  },
  components: {
    FeeDetail,
    DialogConfirm,
  },
  mounted() {
    this.loadData();
  },
  methods: {
    btnAddOnClick() {
      this.formMode = "ADD";
      this.formDetail = true;
    },
    
    btnEditOnClick(feeID) {
      this.formMode = "EDIT";
      this.formDetail = true;
      axios
        .get(`https://localhost:44307/api/v1/Fee/${feeID}`)
        .then((res) => {
          this.feeByID = res.data;
          console.log(this.feeByID)
        })
        .catch((res) => {
          alert(res);
        });
    },
    closeForm() {
      this.formMode = null;
      this.formDetail = false;
    },
    loadData() {
      axios
        .get("https://localhost:44307/api/v1/Fee")
        .then((res) => {
          this.listFee = res.data;
        })
        .catch((res) => {
          alert(res);
        });
      axios
        .get("https://localhost:44307/api/v1/FeeGroup")
        .then((res) => {
          this.listFeeGroup = res.data;
        })
        .catch((res) => {
          alert(res);
        });
    },
    btnDeleteOnClick(feeID) {
      this.isHideDialogConfirm = false;
      this.feeId = feeID;
      console.log(this.feeId);
    },
  },
};
</script>
<style>
#fee-list {
  width: 100%;
  height: 100%;
}

.menu {
  display: flex;
  width: 100%;
  height: 40px;
  background-color: #f6f6f6;
  overflow: hidden;
}

.menu .menu__tab {
  height: calc(40px - 3px);
  padding-left: 20px;
  padding-right: 20px;
  color: #cccccc;
  border-top: 3px solid #e9e9e9;
  display: flex;
  align-items: center;
  cursor: pointer;
}

.menu .menu__tab.current--tab {
  border-top: 3px solid #0e9d50;
  color: #0e9d50;
  background-color: #fff;
  z-index: 3;
}

.option {
  width: calc(100% - 24px);
  height: 50px;
  padding: 0px 12px 0px 12px;
}

.option .option__item {
  height: 100%;
  display: flex;
  align-items: center;
  margin-left: 8px;
}

.option .option__item.float--left {
  margin-left: 0;
}

.table {
  width: calc(100% - 24px);
  height: calc(100% - 40px - 50px - 50px);
  margin: 0 12px 0 12px;
  overflow-x: auto;
  overflow-y: hidden;
}

.table::-webkit-scrollbar {
  width: 6px;
  height: 6px;
  background-color: #e9ebee;
}

.table::-webkit-scrollbar-thumb {
  width: 6px;
  height: 6px;
  background-color: #bbbbbb;
}

.table table {
  width: 100%;
  min-width: 1700px;
  height: 100%;
  border-collapse: collapse;
  border: 1px solid #e9e9e9;
}

.table table thead {
  width: calc(100% - 8px);
  background-color: #0e9d50;
}

.table table tbody {
  display: block;
  height: calc(100% - 10px);
  overflow-y: auto;
  overflow-x: hidden;
}

.table table tbody::-webkit-scrollbar {
  width: 6px;
  height: 6px;
  background-color: #e9ebee;
}

.table table tbody::-webkit-scrollbar-thumb {
  width: 6px;
  height: 6px;
  background-color: #bbbbbb;
}

.table table tr {
  width: 100%;
  cursor: pointer;
  display: table;
  table-layout: fixed;
  height: 40px;
}

.table table td,
.table table th {
  border: 1px solid #e9e9e9;
}

.table .odd-row {
  background-color: #f5f6fa !important;
}

.footer {
  width: calc(100% - 24px);
  height: 50px;
  padding: 0 12px 0 12px;
  display: flex;
  align-items: center;
}
</style>