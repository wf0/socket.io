<template>
  <div class="common">
    <!-- 登录 -->
    <div v-if="!isLogin" class="login">
      <el-tabs v-model="activeName">
        <el-tab-pane label="欢迎登录" name="first">
          <!-- 用户名输入 -->
            <el-input v-model="username" placeholder="请输入用户名">
              <el-button slot="append" @click="login">登录</el-button>
            </el-input>
            <!-- 头像选择 -->
            <div class="avatar">
              <span @click="avatar(src)" v-for="(src,index) in avatarList" :key="index">
                <el-avatar :src="src" :class="{'choosed':src==choosed}"></el-avatar>
              </span>
            </div>
        </el-tab-pane>
      </el-tabs>
    </div>
    <!-- 聊天 -->
    <el-container v-else>
      <el-aside width="350px">
        <my-aside />
      </el-aside>
      <el-container>
        <el-header> 
          <span v-if="userInfo.name=='默认群聊'">{{userInfo.name}}： {{userList.length-1}}</span>  
          <span v-else>{{userInfo.name}}</span>
          </el-header>
        <el-main>
          <my-main />
        </el-main>
        <el-footer>
          <my-footer />
        </el-footer>
      </el-container>
    </el-container>
  </div>
</template>

<script>
import store from './store/index'
import myAside from "./components/myAside.vue";
import myMain from "./components/myMain.vue";
import myFooter from "./components/myFooter.vue";
export default {
  name: "App",
  components: { myAside, myMain, myFooter },
  data() {
    return {
      activeName:'first',
      username:'',
      choosed:'',
      avatarList:[
        'http://img.mp.itc.cn/upload/20170808/5861bc790e654d56bc9289c567b44875_th.jpg',
        'https://cube.elemecdn.com/0/88/03b0d39583f48206768a7534e55bcpng.png',
        'http://5b0988e595225.cdn.sohucs.com/images/20180312/366885f17a20469587cac376c0102527.jpeg',
        'http://img.52z.com/upload/news/image/20180111/20180111085521_86389.jpg',
        'https://pic.qqtn.com/up/2019-1/2019010208201525732.jpg',
        'http://img.zcool.cn/community/010e9557f74cbba84a0d304faa657c.jpg',
        'http://img.zcool.cn/community/01a7f7590cd5a3a8012145509a8335.jpg@2o.jpg',
        'http://5b0988e595225.cdn.sohucs.com/images/20180526/60a21af360d2457c950295839bea8feb.jpeg'
      ],
    }
  },

  methods: {
    avatar(src){
      this.choosed=src
    },
    login(){
      if(this.username&&this.choosed){
        /* 连接socket */
        this.$socket.connect();
        this.$socket.emit('login',{name:this.username,img:this.choosed},(result)=>{
          if(result){
            /* 登录成功的情况下，才修改vuex的数据 */
            this.$message.success('登录成功！');
            /* 告诉vuex修改个人信息 */
            store.commit('setMyInfo',{
              img:this.choosed,
              name:this.username,
            });
            
          }else{
            /* 失败，给出提示！ */
            this.$message.error('用户名重复！');
          }
        });
      }
    },
  },
  computed:{
    isLogin(){
      return store.state.isLogin;
    },
    userInfo(){
      return store.state.userInfo;
    },
    userList(){
      return store.state.userList;
    },
  },
};
</script>
<style scoped>
.choosed{
  border:  solid 2px red;
}
.login{
  width: 50%;
  margin-left: 25%;
  border: solid 1px rgb(228, 231, 237);
  padding: 30px;
}
.login .avatar{
  margin-top: 20px;
}
.login .avatar .el-avatar{
  cursor: pointer;
  margin-left: 5px;
}
.common {
  /* padding: 100px; */
  margin-top: 100px;
  /* width: 800px; */ /* 800 是我为了演示3个用户同时在线，建议设为100%，项目的宽度直接受这个影响*/
  width:100%;
  height: 200px;
}
.el-header,
.el-footer {
  background-color: #b3c0d1;
  color: #333;
  text-align: center;
  line-height: 60px;
}

.el-aside {
  background-color: #d3dce6;
  color: #333;
  text-align: center;
  height: 500px;
}

.el-main {
  background-color: #e9eef3;
  color: #333;
  height: 280px;
}

body > .el-container {
  margin-bottom: 40px;
}
</style>
