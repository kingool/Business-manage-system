<template>
  <div class="main">
    <div class="header">
      <div class="fl title">商品管理系统</div>
      <div class="fr clearfix">
        <div class="user-img fl">
          <img class="auto-img" :src="staticUrl + '/' + userInfo.userImg" alt />
        </div>
        <div class="nickname fl">{{userInfo.nickname}}</div>
        <div class="fl setting">设置</div>
        <div class="fl logout" @click="logout">安全退出</div>
      </div>
    </div>
    <div class="box">
      <div class="aside">
        <!-- 手风琴 -->
        <div class="accordion" id="accordion">
          <div class="card" v-for="(item, index) in asideMenu" :key="index">
            <div class="card-header">
              <div class="aside-item" data-toggle="collapse" :data-target="'#c' + index">
                <div class="fl fa-box">
                  <i class="fa" :class="[item.icon]"></i>
                </div>
                <div class="fl fa-title">
                  <span class="fl">{{item.title}}</span>
                  <i class="fa fa-angle-down fr down-arrow"></i>
                </div>
              </div>
            </div>

            <div
              :id="'c' + index"
              class="collapse c-box"
              :class="{show: index == 0}"
              data-parent="#accordion"
            >
              <div class="card-body">
                <div
                  class="type-item"
                  :class="{active: v.isActive}"
                  v-for="(v, i) in item.subTitle"
                  :key="i"
                  @click="toggleAsideMenu(v)"
                >{{v.name}}</div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="content">
        <!-- 二级路由 -->
        <router-view></router-view>
      </div>
    </div>
  </div>
</template>

<script>
import MsgBox from "../components/MsgBox";
export default {
  name: "Main",

  data() {
    return {
      asideMenu: [
        {
          title: "数据统计",
          icon: "fa-bar-chart",
          subTitle: [
            {
              name: "商品统计",
              isActive: true,
              routerName: "Echarts"
            },
            {
              name: "订单统计",
              isActive: false,
              routerName: ""
            }
          ]
        },
        {
          title: "商品管理",
          icon: "fa-cube",
          subTitle: [
            {
              name: "商品类型",
              isActive: false,
              //路由名称
              routerName: "Type"
            },
            {
              name: "商品列表",
              isActive: false,
              routerName: "Products"
            }
          ]
        },
        {
          title: "订单管理",
          icon: "fa-file-text-o",
          subTitle: [
            {
              name: "订单列表",
              isActive: false,
              routerName: ""
            }
          ]
        },
      ],
      userInfo: {},
      // logout: '退出登录成'
    };
  },

  created() {
    console.log("this.staticUrl ==> ", this.staticUrl);
    this.getUserInfo();
  },

  methods: {
    //获取用户信息
    getUserInfo() {
      this.axios({
        method: "GET",
        url: "/userInfo"
      })
        .then(result => {
          // console.log('result ==> ', result);
          if (result.data.code == 1060) {
            this.userInfo = result.data.result[0];
          }
        })
        .catch(err => {
          console.log("err => ", err);
        });
    },

    //退出登录
    logout() {
      // this.$showToast({
      //   message: this.logout,
      //   duration: 3000
      // });
      this.$cookies.remove("_abc");
      this.$router.push({ name: "Login" });
    },

    //切换侧边栏菜单
    toggleAsideMenu(item) {
      //如果当前选中，不做任何事情
      if (item.isActive) {
        console.log("已经选中");
        return;
      }
      console.log("item ==> ", item);

      //将其他选中修改为未选中状态
      this.asideMenu.forEach(v1 => {
        v1.subTitle.forEach(v2 => {
          v2.isActive = false;
        });
      });

      item.isActive = true;

      this.$router.push({ name: item.routerName });
    }
  },
  components: {
    MsgBox
  }
};
</script>

<style lang="less" scoped>
.fa-box {
  width: 16px;
  height: 16px;
  margin-top: 10px;
}
.setting {
  margin: 0 20px;
  cursor: pointer;
}
.logout {
  cursor: pointer;
}
.fa-title {
  margin-left: 10px;
  width: 154px;
  line-height: 44px;
}

.down-arrow {
  line-height: 44px;
  font-size: 18px;
  margin-right: 20px;
}
.user-img {
  width: 60px;
  height: 60px;
  margin-top: 10px;
  border-radius: 50%;
  background-color: #fff;
  overflow: hidden;
}
.nickname {
  margin-left: 20px;
}
.title {
  font-size: 24px;
}
.type-item {
  height: 40px;
  line-height: 40px;
  color: #fff;
  padding-left: 46px;
  cursor: pointer;
  &:hover {
    background-color: #535459;
  }
}

.type-item.active {
  background-color: #262630;
}
.card-body {
  padding: 0;
}
.aside-item {
  height: 44px;
}
.card-header {
  padding: 0;
  background-color: #535459;
}
.card {
  border-radius: 0;
  background-color: transparent;
  border: none;
  border-bottom: 1px solid rgba(0, 0, 0, 0.125);
}
.header {
  height: 80px;
  background-color: #262630;
  position: sticky;
  top: 0;
  padding: 0 20px;
  color: #fff;
  line-height: 80px;
}
.aside {
  position: fixed;
  left: 0;
  top: 80px;
  bottom: 0;
  width: 200px;
  background-color: #35353f;
}

.aside-item {
  height: 44px;
  color: #fff;
  padding-left: 20px;
  cursor: pointer;
}

.content {
  width: calc(100% - 200px);
  margin-left: 200px;
  background-color: #fff;
  padding: 20px;
}
</style>