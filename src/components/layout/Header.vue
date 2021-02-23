<template>
    <div id="header">
        <div class="item float--left header__title">
            <div class="title__text">Lập kế hoạch thu</div>
            <div class="title__icon"></div>
        </div>
        <div class="item float--right">
            <div class="account icon-avatar"></div>
        </div>
        <div class="item float--right">
            <div class="search icon-search"></div>
        </div>
        <div class="item float--right">
            <select class="year m-select">
                <option class="m-option">2020-2021</option>
                <option class="m-option">2019-2020</option>
                <option class="m-option">2018-2019</option>
            </select>
        </div>
        <div class="item float--right m-flex">
            <div class="home m-flex">
                <div class="icon icon-home"></div>
                <div class="text">Trường mẫu giáo Edu platform 1</div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    
}
</script>
<style scoped>
@import "../../styles/layouts/header.css";
</style>