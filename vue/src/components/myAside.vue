<template>
  <div>
    <el-row style="height: 60px">
      <!-- 我的信息 -->
      <div class="myinfo">
        <el-avatar :src="myInfo.img"></el-avatar>
        <span>{{myInfo.name}}</span>
      </div>
    </el-row>
    <!-- 搜索好友 -->
    <el-row style="height: 50px">
      <el-input v-model="keyword" placeholder="搜索好友">
        <el-button slot="append" icon="el-icon-search"></el-button>
      </el-input>
    </el-row>
    <!-- 好友列表 -->
    <el-row style="height: 390px">
      <el-table @cell-click="setUserInfo" :data="tableData.filter(data => (!keyword||(data.name.toLowerCase().includes(keyword.toLowerCase())))&&!(data.name.toLowerCase().includes(myInfo.name.toLowerCase())))" height='390px' stripe style="width: 100%" :show-header='false'>
        <el-table-column  label="日期" >
          <template slot-scope="scope">
            <div class="userlist">
                <el-avatar :src="scope.row.img"></el-avatar>
                <span>{{ scope.row.name }}</span>
            </div>
          </template>
        </el-table-column>
      </el-table>
    </el-row>
  </div>
</template>

<script>
import store from '../store/index'
export default {
  data() {
    return {
      keyword: "",
    };
  },
  methods: {
    setUserInfo(row, column, event){
      if(row.name=='默认群聊'){
        store.commit('changeChatType','group');
      }else{
        store.commit('changeChatType','private');
      }
      store.commit('setUserInfo',{name:row.name,img:row.img});
    },
  },
  computed:{
    tableData(){
      return store.state.userList;
    },
    myInfo(){
      return store.state.myInfo;
    },
  },
};
</script>
<style scoped>
.myinfo{
  text-align: left;
  vertical-align: middle;
  margin-top: 10px;
  margin-left: 10px;
}
.myinfo span{
  text-align: left;
  vertical-align: middle;
}
.userlist{
  vertical-align: middle;
  cursor: pointer;
}
.userlist span{
  vertical-align: middle;
  margin-left: 10px;
}
</style>