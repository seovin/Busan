<template>
  <main>
    <div id="carouselExampleCaptions" class="carousel slide carousel-fade" data-bs-ride="carousel">
            <div class="carousel-inner">
                <div class="carousel-item active head">
                    <img src="https://cdn.pixabay.com/photo/2020/03/12/14/24/busan-4925217_1280.jpg" class="d-block w-100" >
                    <div class="d-none d-md-block carousel_subtext">
                        <div class="subject">
                            <p>커뮤니티</p>
                            <p style="font-size: 30px;">community</p>
                        </div>
                    </div>
                </div>
            </div>
    </div>
    <div class="box" style="height: 1500px;">
    <div class="container">
        <br/>
        <h2 class="text-center">커뮤니티 상세페이지</h2>
        <br>
        <div style="float:right">
            <router-link to="/community"><input class="btn btn-outline-secondary btn-sm" type="button" value="목록"></router-link>
            <input class="btn btn-outline-secondary btn-sm" type="button" value="수정" @click="setMode()">
            <input class="btn btn-outline-secondary btn-sm" type="button" value="삭제" @click="delPosts()">
        </div>
        <br>
        <br>
        <table class="table table-striped">
            <tbody>
                <tr>
                    <th>번호</th>
                    <td>{{items.id}}</td>
                </tr>
                <tr>
                    <th>작성자</th>
                    <td>{{items.author_name}}</td>
                </tr>
                <tr>
                    <th>조회수</th>
                    <td>{{items.readcnt}}</td>
                </tr>
                <tr>
                    <th>작성일자</th>
                    <td>{{items.dt_created}}</td>
                </tr>
                <!-- <tr>
                    <th>제목</th>
                    <td>{{items.title}}</td>
                </tr>
                <tr>
                    <th>내용</th>
                    <td colspan="2">
                        <div class="content">{{items.content}}<br><br></div>
                    </td>
                </tr> -->
            </tbody>
        </table>
        <form>
            <div>
                <div class="mb-3">
                    <label class="form-label" for="title">제목</label>
                    <input class="form-control" type="text" name="title" id="title" v-model="items.title" :disabled="disabled"/>
                </div>
                <div class="mb-3">
                    <label class="form-label" for="content">내용</label> 
                    <textarea class="form-control"  name="content" id="content" v-model="items.content" :disabled="disabled"></textarea>
                </div>
                <!-- <input type="hidden" id="token" name="token" :value="headers"> -->
                <div v-if="!disabled">
                <button class="btn btn-outline-secondary btn-sm" type="button" @click="setPosts()">저장</button>
                </div>
            </div>
        </form>
        <br>
        <nav>
            <ul class="pagination justify-content-center">
                <li class="page-item mr-3">
                    <!-- <router-link :to="'/communitydetail/' + item.id">{{item.title}}</router-link> -->
                    <!-- <router-link :to="'/communitydetail/' + prePage"><p class="page-link">&larr; Prev</p></router-link> -->
                    <p class="page-link" @click="junPage">&larr; Prev</p>
                </li>
                <li class="page-item">
                    <p class="page-link" @click="daumPage">Next &rarr;</p>
                </li>
            </ul>
        </nav> 
    </div>
    </div>
        <ModalView v-if="showModal" @close="showModal = false">
      <!--
        you can use custom content here to overwrite
        default content
      -->
          <h3 slot="header">성공!</h3>
          <div slot="body">정상적으로 수정(삭제)되었습니다.</div>
        </ModalView>
  </main>
</template>

<script>
import axios from 'axios';
import ModalView from './ModalView.vue';

export default {
 data() {
     return {
         disabled: true,
         items: [],
         token: this.$attrs.propsdata.token,
         showModal: false
     }
 },
//  created(){
//      axios.get(`/api/v1/board/${this.$route.params.id}/`)
//      .then((response) => this.items = response.data)
//      .catch((err) => console.log(err))
//  },
 mounted () {
    this.handleService()
    this.getPosts()
 },
 computed: {
    prePage () {
      let listLeng = this.items.id
    //   if (listLeng === !1) listLeng -= 1;
      listLeng -= 1;
      return listLeng;
    },
    nextPage () {
      let nextNum = this.items.id
    //   if (listLeng === !1) listLeng -= 1;
      nextNum += 1;
      return nextNum;
    },
 },
 methods: {
    handleService() {
      this.readMode()
      this.getPosts()
    },
    getPosts() {
     axios.get(`/api/v1/board/${this.$route.params.id}/`)
     .then((response) => this.items = response.data)
     .catch((err) => console.log(err))
    },
    readMode() {
      this.disabled = true
    },
    setMode() {
      this.disabled = false
    },
    setPosts() {
      let params = {
        "title" : this.items.title,
        "content" : this.items.content,
      }
      axios.put(`/api/v1/board/${this.$route.params.id}/`,
      JSON.stringify(params), {headers: { 'content-type':
      'application/json',
      // 나중에 받아올 토큰값을 적는다. 현재 임시로 test3의 토큰값을 적어놓음.
      // 'Authorization': 'token d548c2da5d0b412017c3bad825397d0427f8e956ff7d45c4b9b940d05976458d'
      'Authorization': `token ${this.token}` 
      }}
      ).then(res => {
        console.log(res.data)
        this.showModal = !this.showModal;
        
        this.handleService()
      }).catch(e => {
        console.log(e);
        alert("내용을 입력하시오.")
      })
    },
    delPosts() {
      axios.delete(`/api/v1/board/${this.$route.params.id}/`, {headers: { 'content-type':
      'application/json',
      // 나중에 받아올 토큰값을 적는다. 현재 임시로 test3의 토큰값을 적어놓음.
      // 'Authorization': 'token d548c2da5d0b412017c3bad825397d0427f8e956ff7d45c4b9b940d05976458d'
      'Authorization': `token ${this.token}` 
      }})
      .then(res => {
        console.log(res.data)
        this.showModal = !this.showModal;
        this.$router.push("/community")
      }).catch(e => {
        console.log(e);
        alert("삭제가 실패했습니다.")
      })
    },
    junPage() {
        let junNum = this.items.id - 1;
        this.$router.push(`/communitydetail/${junNum}`);
        this.getPosts();
    },
    daumPage() {
        let daumNum = this.items.id + 1;
        this.$router.push(`/communitydetail/${daumNum}`);
        this.getPosts();
    }
 },
 components: {
  ModalView : ModalView
 }
}
</script>

<style>

</style>