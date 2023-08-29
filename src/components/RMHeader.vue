<template>
    <div class="headers">
        <span class="colImg">Photo</span>
        <span class="col">Character ID</span>
        <span class="colName">Name</span>
        <span class="col">Gender</span>
        <span class="col">Species</span>
        <span class="col">Last Episode</span>
        <span class="colInfo">Info</span>
        <span class="colFav">Add To Favorites</span>
    </div>
</template>

<script lang="ts">
export default ({
    name: 'RMHeader',
});
</script>

<style scoped lang="scss">
@import "@/styles/variables.scss";

.headers {
    width: 100%;
    display: flex;
    align-items: center;
    background: rgba($line-color, 0.25);
    padding: 16px 0;
    color: $primary-color;
    font-size: 16px;

    @include lg {
        font-size: 10px;
    }
}

.col {
    width: 15%;
    display: block;

    @include lg {
        display: none;
    }
}

.colName {
    width: 20%;
    display: block;

    @include lg {
        display: none;
    }
}

.colImg {
    padding-left: 140px;
    width: 10%;

    @include lg {
        padding-left: 20px;
        width: 20%;
    }
}

.colFav {
    width: 15%;

    @include lg {
        width: 25%;
    }
}

.colInfo {
    display: none;
    flex: 1;

    @include lg {
        display: flex;
    }
}
</style>