<template>
    <div class="overlay"></div>
    <div class="confirm-view">
        <div class="main-text">
            정말 삭제하시겠습니까?
        </div>
        <div class="btn-group">
            <button class="btn cancel" @click="$emit('close')">취소</button>
            <button class="btn delete" @click="$emit('delete')">삭제</button>
        </div>

    </div>
</template>

<script setup>

</script>

<style lang="scss" scoped>
    .overlay {
        background-color: rgba($color: #000000, $alpha: 0.5);
        position: fixed;
        left: 0;
        top: 0;
        width: 100%;
        height: 100%;
        overflow: hidden;
    }
    .confirm-view {
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        width: 300px;
        height: 150px;
        border-radius: 10px;
        position: fixed;
        left: 50%;
        top: 50%;
        transform: translate(-50%,-50%);
        background-color: #fff;
        text-align: center;

        .main-text {
            padding: 30px;
            font-weight: bold;
        }

        .btn-group {
            display: flex;
            margin-bottom: 10px;
        }
    }
</style>