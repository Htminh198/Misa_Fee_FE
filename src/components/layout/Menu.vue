<template>
    <div id="menu">
        <div class="menu__logo">
            <div class="logo"></div>
            <div class="title">Khoản thu</div>
            <div class="icon--bar"></div>
        </div>
        <div class="menu__content">
            <div v-for="item in listItem" :key="item.key" :class="{item: true, 'item--on--select': item.key===current}" >
                <div :class="'item__icon icon-' + item.key"></div>
                <div class="item__text">{{item.title}}</div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    data() {
        return {
            current: "planning-fees",
            listItem: [
                {
                    key: "dashboard",
                    title: "Tổng quan"
                },
                {
                    key: "planning-fees",
                    title: "Lập kế hoạch thu"
                },
                {
                    key: "manage-fees",
                    title: "Quản lý thu"
                },
                {
                    key: "manage-bill",
                    title: "Quản lý hóa đơn"
                },
                {
                    key: "receivable-payable",
                    title: "Số phải thu, phải trả"
                },
                {
                    key: "report",
                    title: "Báo cáo"
                },
                {
                    key: "message",
                    title: "Tin nhắn"
                },
                {
                    key: "system",
                    title: " Hệ thống"
                }
            ]
        }
    },
}
</script>
<style scoped>
@import "../../styles/layouts/menu.css";
</style>