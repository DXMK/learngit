<template>
  <div id="app">
    <!-- <img src="./assets/logo.png"> -->
    <el-row>
      <el-col :span="3" class="left">
        <el-menu class="left">
          <el-menu-item index @click="getpost">
            <template slot="title">每日推荐</template>
          </el-menu-item>
          <el-menu-item index>
            <template slot="title">主题日报</template>
          </el-menu-item>
        </el-menu>
      </el-col>
      <el-col :span="6">
        <table>
          <tr>
            <td colspan="2">{{today.date}}</td>
          </tr>
          <tr
            v-for="todd in this.today.stories"
            :key="todd.id"
            @click="chang(todd.id)"
            class="tr-1"
          >
            <td>
              <img :src="'./static/img/'+todd.images" class="img-1">
            </td>
            <td>{{todd.title}}</td>
          </tr>
        </table>
      </el-col>
      <!-- 主体内容 -->
      <el-col :span="15" v-show="people!=''">
        <el-row>
          <el-col :span="24">
            <img :src="'./static/img/'+people.images" class="img-2">
          </el-col>
        </el-row>
        <p class="font-1">{{people.title}}</p>
        <p class="font-2">{{people.question_title}}</p>
        <el-row>
          <el-col :span="24" class="col-1">
            <img :src="'./static/img/'+people.question_t_src" width="25" class="img-3" v-show="people.question_t_src">
            <span>{{people.author}}</span>
            <font color="#9AA0AD">{{people.bio}}</font>
          </el-col>
        </el-row>
        <el-row>
          <div v-html="people.content" class="con"></div>
        </el-row>
        <el-row v-for="sch in this.people.schema" :key="sch.id">
          <div v-html="sch"></div>
          <br>
        </el-row>

        <!-- 第三部分主体 -->
        <div>
          <p class="font-1">{{people.question_tle}}</p>
          <div v-for="ques in questions" :key="ques.id">
            <h3>{{ques.question_title}}</h3>
            <div>
              <img :src="'./static/img/'+ques.question_title_src" width="25" class="img-3">
              <span>{{ques.author}}</span>
              <font color="#9AA0AD">{{ques.bio}}</font>
            </div>
            <div v-html="ques.content"></div>
            <div>
              <a :href="ques['view-more']" class="a-1">{{ques.look}}</a>
            </div>
            <hr class="hl">
          </div>
        </div>

        <!-- 评论 -->
        <div class="comment">
          <div class="see" @click="dis(people.id)">查看知乎评论</div>
          <div v-show="comms!=''">评论({{cl}})</div>
          <div v-for="(com,index) in comms" :key="com.id" class="comment-d">
            <p>
              <img :src="'./static/img/'+com.avatar" alt class="img-4">
              <span>{{com.author}}</span>
            </p>
            <p>{{times[index]}}天前</p>
            <p>{{com.content}}</p>
            <hr style="border:1px dotted #000000">
          </div>
        </div>
      </el-col>
    </el-row>
    <router-view/>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      today: [],
      people: [],
      comms: [],
      questions: [],
      cl: ""
    };
  },
  methods: {
    getpost() {
      this.$http
        .get("/static/news.json")
        .then(resp => {
          this.today = resp.data;
          // console.log(this.today);
        })
        .catch(error => {
          console.log(`请求失败：${error}`);
        });
    },
    chang(id) {
      this.$http
        .get("/static/" + id + "-news.json")
        .then(resp => {
          this.people = resp.data;
          this.questions = resp.data.questions;
          // console.log(this.people);
        })
        .catch(error => {
          console.log(`请求失败：${error}`);
        });
    },
    dis(id) {
      this.$http
        .get("/static/" + id + "-comment.json")
        .then(resp => {
          this.comms = resp.data.comments;
          this.cl = resp.data.comments.length;
          // console.log(this.comms)
        })
        .catch(error => {
          console.log(`请求失败：${error}`);
        });
    }
  },
  watch: {
    people: {
      handler() {
        this.comms = [];
      }
    }
  },
  computed: {
    times: function() {
      let ti = [];
      for (var i = 0; i < this.comms.length; i++) {
        ti[i] = parseInt(this.comms[i].time / 1000 / 60 / 60 / 24);
      }
      return ti;
    }
  }
};
</script>

<style>
body {
  margin: 0px;
  padding: 0px;
}
a {
  position: relative;
  left: 600px;
  color: #7797cb;
  text-decoration: none;
}
#app {
  /* font-family: "Avenir", Helvetica, Arial, sans-serif; */
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin-top: 0px;
}
.hl {
  border: none;
  height: 5px;
  background-color: rgb(233, 233, 233);
}
.comment-d p {
  margin: 0px;
  margin-top: 5px;
  padding: 0px;
  text-indent: 50px;
}
.see {
  cursor: pointer;
  background-color: #f4f6f8;
  text-align: center;
}
.left {
  text-align: center;
  font-size: 40px;
  background-color: rgb(248, 248, 248);
  border: none;
  height: 300px;
}
.col-1 {
  position: relative;
  bottom: 10px;
}
.img-1 {
  width: 80px;
}
.img-2 {
  width: 100%;
  position: relative;
  left: 0px;
  height: 400px;
}
.img-3 {
  position: relative;
  top: 5px;
}
.img-4 {
  position: relative;
  top: 5px;
  width: 20px;
}
.p-1 {
  /* margin-top: 0px; */
  padding: 0px;
  text-align: left;
}
tr {
  height: 50px;
}
.tr-1 {
  cursor: pointer;
  text-align: left;
}
.font-1 {
  width: 500px;
  margin: 0px;
  padding: 0px;
  color: white;
  font-weight: bold;
  font-size: 25px;
  position: relative;
  bottom: 100px;
  left: 150px;
}
.font-2 {
  width: 600px;
  margin: 0px;
  padding: 0px;
  font-weight: bold;
  font-size: 25px;
  position: relative;
  bottom: 50px;
  left: 150px;
}
</style>
