<template>
    <div ref="parent" class="gamemap">
        <!-- tabindex -> 用户操作 -->
        <canvas ref="canvas" tabindex="0">

        </canvas>
    </div>

</template>

<script>
import {GameMap} from '@/assets/scripts/GameMap';
import { ref, onMounted } from 'vue';
import { useStore } from 'vuex';

export default {
    setup() {
        const store = useStore();
        // 指向div
        let parent = ref(null);
        let canvas = ref(null);

        onMounted(() => {
            store.commit("updateGameObject",
                new GameMap(canvas.value.getContext('2d'), parent.value, store));
        });

        return {
            parent,
            canvas
        }
    }
}
</script>

<style scoped> 
div.gamemap {
    height: 100%;
    width: 100%;
    display: flex;
    /* 水平居中 */
    justify-content: center;
    /* 竖直居中 */
    align-items: center;
}
</style>