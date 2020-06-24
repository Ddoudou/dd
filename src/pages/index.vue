<template>
  <div id="index">
    <router-view></router-view>
    <div class="content">
      <van-cell-group>
        <van-field v-model="matchID" type="digit" label="比赛ID" placeholder="请输入比赛ID">
          <template #button>
            <van-button size="small" type="primary" @click="onLoad">确定</van-button>
          </template>
        </van-field>
      </van-cell-group>
      <van-collapse v-model="activeNames">
        <van-collapse-item title="玩家列表" name="1">
          <van-collapse v-model="playerListCollapse">
            <van-collapse-item
              v-for="item in playerList"
              :key="item.personaname"
              :name="item.index"
              :is-link="false"
              accordion
            >
              <template #title>
                <van-row type="flex" align="center" justify="center">
                  <van-col span="6">
                    <van-image round width="3rem" height="3rem" fit="contain" :src="item.ava"/>
                  </van-col>
                  <van-col span="18">{{item.personaname}}</van-col>
                </van-row>
              </template>
            </van-collapse-item>
          </van-collapse>
        </van-collapse-item>
      </van-collapse>
    </div>
  </div>
</template>

<script>
// import jsonp from 'jsonp'
import axios from "axios";
import Vue from "vue";
import { Collapse, CollapseItem } from "vant";
import { Field } from "vant";
import { List } from "vant";
import { Cell, CellGroup } from "vant";
import { Button } from "vant";
import { Toast } from "vant";
import { Image as VanImage } from "vant";
import { Col, Row } from "vant";

Vue.use(Col);
Vue.use(Row);
Vue.use(VanImage);
Vue.use(Toast);
Vue.use(Button);
Vue.use(Cell);
Vue.use(CellGroup);
Vue.use(Field);
Vue.use(Collapse);
Vue.use(CollapseItem);
Vue.use(List);

export default {
  name: "index",
    components:{
    },
  data() {
    return {
      matchID: "",
      activeNames: ["0"],
      playerList: [],
      playerListCollapse: []
    };
  },

  methods: {
    onLoad() {
      Toast.loading({
        message: "比赛信息加载中...",
        forbidClick: true
      });
      var that = this;
      let baseUrl = "https://api.opendota.com/api/";
      var matchUrl = baseUrl + "matches/" + that.matchID;

      // 获取比赛信息
      axios.get(matchUrl).then(function(res) {
        var playerListBase = res.data.players;
        Toast.clear();
        playerListBase.forEach(function(value, index, arr) {
          var playerUrl = baseUrl + "players/" + value.account_id;

          // 获取玩家头像
          axios.get(playerUrl).then(function(res) {
            arr[index].ava = res.data.profile.avatarfull;
          });
        });
        that.playerList = playerListBase;
      });
    }
  }
};
</script>


<style>
#index {
  background-image: url(https://cdn.jsdelivr.net/gh/Ddoudou/ddoudou.github.io/static/img/bg0.webp);
  background-repeat: no-repeat;
  background-size: cover;
  min-height: 100vh;
}
.content {
  max-width: 500px;
  margin: 0 auto;
  min-height: 100vh;
  background-color: white;
}
.padding0 {
  padding: 0;
}
</style>
