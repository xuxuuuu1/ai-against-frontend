<template>
    <div class="matchground">
        <div class="row">
            <!-- 自己 -->
            <div class="col-4">
                <div class="user-photo">
                    <img :src="$store.state.user.photo" alt="">
                </div>
                <div class="user-username">
                    {{ $store.state.user.username }}
                </div>
            </div>

            <div class="col-4">
                <div class="user-select-bot">
                    <select v-model="select_bot" class="form-select" aria-label="Default select example">
                        <option value="-1" selected>亲自出马</option>
                        <option v-for="bot in bots" :key="bot.id" :value="bot.id">
                            {{ bot.title }}
                        </option>
                    </select>
                </div>
            </div>


            <!-- 敌人 -->
            <div class="col-4">
                <div class="user-photo">
                    <img :src="$store.state.pk.opponent_photo" alt="">
                </div>
                <div class="user-username">
                    {{ $store.state.pk.opponent_username }}
                </div>
            </div>

            <div class="col-12 match-btn">
                <button @click="click_match_btn" type="button" class="btn btn-info btn-lg">{{ match_btn_info }}</button>
            </div>

        </div>
    </div>
</template>

<script>

import { ref } from 'vue';
import { useStore } from 'vuex';
import $ from 'jquery'

export default {
    
    setup() {
        let match_btn_info = ref("开始匹配");
        const store = useStore();
        let bots = ref([]);
        // 双向数据绑定
        let select_bot = ref("-1");

        const click_match_btn = () => {
            if (match_btn_info.value === "开始匹配") {
                match_btn_info.value = "取消匹配";
                console.log(select_bot.value);
                store.state.pk.socket.send(JSON.stringify({
                    event:"start-matching",
                    bot_id:select_bot.value,
                }));
                
            } else {
                match_btn_info.value = "开始匹配";
                store.state.pk.socket.send(JSON.stringify({
                    event:"stop-matching",
                }));
            }
        }

        const refresh_bots = () => {
            $.ajax({
                url:'https://43.143.193.15:3000/user/bot/getlist/',
                type:'get',
                headers:{
                    Authorization:"Bearer " + store.state.user.token,
                },
                success(resp) {
                    console.log("refresh_success!");
                    bots.value = resp;
                }
            })
        }
        refresh_bots(); // 从云端获取bots

        return {
            match_btn_info,
            click_match_btn,
            bots,
            select_bot,
        }
    }
    
}
</script>

<style scoped>

div.match-btn {
    text-align: center;
    padding-top: 10vh;
}

div.matchground {
    width: 60vw;
    height: 70vh;
    /* background: lightblue; */
    margin: 40px auto;
    background-color: rgba(50, 50, 50, 0.5);
}

div.user-photo {
    text-align: center;
    padding-top: 10vh;
}

div.user-photo > img {
    text-align: center;
    width: 25vh;
}

div.user-username {
    text-align: center;
    color: white;
    font-size: 24px;
    font-weight: 600;
    padding-top: 2vh;
}

div > img {
    border-radius: 50%;
}

div.user-select-bot {
    padding-top: 20vh;
}

div.user-select-bot > select {
    margin: 0 auto;
    width: 60%;
}
</style>