<template>
    <div>
      <a-layout style="background: white">
        <a-layout-sider style="background: white; margin-left: 2%;">
          <img src="../assets/logo.png" width="120%" @click="to_user('/')"/>
        </a-layout-sider>
        <a-layout-content style="margin-top: 0.5%; margin-left: 4%">
         <a-menu v-model="seletedKeys" mode="horizontal">

           <a-menu-item key="1" @click="to_goods_list_page('vga')" style="font-size: 16px">显卡</a-menu-item>
           <a-menu-item key="2" @click="to_goods_list_page('memory')" style="font-size: 16px">内存</a-menu-item>
           <a-menu-item key="3" @click="to_goods_list_page('motherboard')" style="font-size: 16px">主板</a-menu-item>
           <a-menu-item key="4" @click="to_goods_list_page('cpu')" style="font-size: 16px">处理器</a-menu-item>
           <a-menu-item key="5" @click="to_goods_list_page('hard_drives')" style="font-size: 16px">硬盘</a-menu-item>
           <a-menu-item key="6" @click="to_goods_list_page('case')" style="font-size: 16px">机箱</a-menu-item>
           <a-menu-item key="7" @click="to_goods_list_page('power')" style="font-size: 16px">电源</a-menu-item>

            <a-popover style="margin-left: 2%; font-size: 16px">
              <template slot="content">
                <p>你好：{{username}}</p>
              </template>
              <a-avatar :src="avatar" style="width: 3.5%; height: 3.5%; margin-left: 50%; margin-bottom: 1%" @click="to_user('/user')"/>
            </a-popover>
           <a-input-search placeholder="输入搜索内容" style="width: 20%; margin-left: 1%"
                                        @search="to_search('/goodslist', search_content)" v-model="search_content"/>
          </a-menu>
        </a-layout-content>
      </a-layout>
    </div>

</template>

<script>
import { EventBus } from '../event-bus'


function sleep (time) {
  return new Promise((resolve) => setTimeout(resolve, time));
}
export default {
    watch: {
      '$route' (val, old) {
        this.updateKeyByURL(val)
      }
    },
    model: {
        prop: 'collapsed',
        event: 'changeMenu',
    },
    data () {
      return {
        choose: 'home',
        username: '未设置用户名',
        avatar: 'https://zos.alipayobjects.com/rmsportal/ODTLcjxAfvqbxHnVXCYX.png',
        search_content: null,
        seletedKeys: []
      }
    },
    props: {
        // close or not.
        collapsed: Boolean
    },
    methods: {
      to_goods_list_page(category) {
        this.$router.push({
          path: '/good?id=1',
        })
        let _this = this
        sleep(100).then(() => {
          _this.$router.push({
            path: '/goodslist',
            query: {
              category: category
            }
          })
        })
      },
      to_user(path) {
        this.choose = 'user'
        this.$router.push(path)
      },
      to_search(path, search_content) {
        this.$router.push({ path: path, query: {searchid: search_content}})
        EventBus.$emit('submitSearch',this.to_search)
      },
      updateKeyByURL (newRoute) {
        console.log("check", newRoute)
        if (newRoute.path !== "/goodslist") {
          this.seletedKeys = []
          return
        }
        switch (newRoute.query.category) {
          case "vga":
            this.seletedKeys = ["1"]; console.log("update key to vga"); break
          case "memory":
            this.seletedKeys = ["2"]; console.log("update key to memory"); break
          case "motherboard":
            this.seletedKeys = ["3"]; console.log("update key to motherboard"); break
          case "cpu":
            this.seletedKeys = ["4"]; console.log("update key to cpu"); break
          case "hard_drives":
            this.seletedKeys = ["5"]; console.log("update key to hard_drives"); break
          case "case":
            this.seletedKeys = ["6"]; console.log("update key to case"); break
          case "power":
            this.seletedKeys = ["7"]; console.log("update key to power"); break
          default:
            this.seletedKeys = []; console.log("update key to none")
        }
      },
    },
    mounted() {
      let _this = this
      this.$axios.get(this.$base_url + 'api/user/getInfo')
        .then(function (response) {
          _this.username = response.data.data.username
          _this.avatar = _this.$base_url + response.data.data.avatar_url
        }).catch(function (error) {
      })
      this.$event_bus.$on('update_username', (new_name) => {
        _this.username = new_name
      })
      this.$event_bus.$on('login_success', (name, avatar_url) => {
        _this.username = name
        _this.avatar = avatar_url
      })
      sleep(100).then(()=> {
        _this.updateKeyByURL(this.$route)
      })
    }
}

</script>

<style scoped>
.trigger {
  font-size: 18px;
  line-height: 64px;
  padding: 0 24px;
  cursor: pointer;
  transition: color 0.3s;
}

.trigger:hover {
  color: #1890ff;
}

</style>
