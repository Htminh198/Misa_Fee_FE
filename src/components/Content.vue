<template>
    <div id="content">
        <FeeListItems />
    </div>
</template>
<script>
import FeeListItems from '../views/planning/FeeListItems'

export default {
    components: {
        FeeListItems
    }
}
</script>
<style>
#content {
    position: absolute;
    top: 50px;
    left: 205px;
    right: 0;
    bottom: 0;
    width: calc(100% - 205px);
    height: calc(100vh - 50px);
}
</style>