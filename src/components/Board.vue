<!--<template>-->
<!--  <div class="bbs doc-area">-->
<!--    -->
<!--    <div class="board-view" v-if="tdata.isNewMode && isWritableTopic()">-->
<!--      <div class="write-info">-->
<!--        <div class="write-name">{{user().name}}</div>-->
<!--        <div class="write-tool">-->
<!--          <span class="bt" @click="writeTopic()">글쓰기</span>-->
<!--        </div>-->
<!--      </div>-->
<!--      <div class="edit-subject">-->
<!--        <input type="text" v-model="tdata.newSubject" placeholder="제목을 입력해주세요." />-->
<!--      </div>-->
<!--      <div class="edit-centent">-->
<!--        <md v-model="tdata.newContent" height="600px" placeholder="내용을 입력해주세요."/>-->
<!--      </div>-->
<!--    </div>-->
<!--    <div class="board-view" v-else-if="view != null">-->
<!--      <div v-if="view.exist">-->
<!--        <div class="board-title" v-if="view.isView">-->
<!--          <div class="board-title-tool" v-if="isEditableTopic() || isDeletableTopic()">-->
<!--            <span class="bt" v-if="isEditableTopic()" @click="view.isView = false">수정</span>-->
<!--            <span class="bt" v-if="isDeletableTopic()" @click="deleteTopic()">삭제</span>-->
<!--          </div>-->
<!--          <div class="board-title-text">{{view.subject}}</div>-->
<!--        </div>-->

<!--        <div v-if="!view.isView" class="board-title-edit">-->
<!--          <input type="text" v-model="view.subject" @keydown.enter="editTopic(view)" placeholder="제목을 입력해주세요." />-->
<!--        </div>-->

<!--        <div v-for="node in view.posts" :key="node.brn">-->
<!--          <div class="write-info post">-->
<!--            <div class="write-name">{{node.name}}</div>-->
<!--            <div class="write-tool">-->
<!--              <span class="bt" v-if="isEditablePost(node) && node.isView" @click="node.isView = false">수정</span>-->
<!--              <span class="bt" v-if="isEditablePost(node) && !node.isView" @click="editPost(node)">수정완료</span>-->
<!--              <span class="bt" v-if="isDeletablePost(node)" @click="deletePost(node)">삭제</span>-->
<!--              <span>{{node.regDt}}</span>-->
<!--            </div>-->
<!--          </div>-->
<!--          <div v-if="node.isView" class="content" v-html="render(node.content)"></div>-->
<!--          <div v-else>-->
<!--            <md v-model="node.content" placeholder="내용을 입력해주세요." />-->
<!--          </div>-->
<!--        </div>-->

<!--        <div class="new-post" v-if="isWritablePost()">-->
<!--          <div class="write-info post">-->
<!--            <div class="write-name">{{user().name}}</div>-->
<!--            <div class="write-tool">-->
<!--              <span class="bt" @click="writePost(view.bn)">댓글쓰기</span>-->
<!--            </div>-->
<!--          </div>-->
<!--          <div class="new-post-md">-->
<!--            <md v-model="tdata.newPostContent" placeholder="내용을 입력해주세요." />-->
<!--          </div>-->
<!--        </div>-->

<!--      </div>-->
<!--      <div class="not-exist-content" v-else>-->
<!--        존재하지 않거나 삭제된 게시물 입니다.-->
<!--      </div>-->
<!--    </div>-->

<!--    <table class="board-topics-table">-->
<!--      <tr class="mob-hide">-->
<!--        <th class="seq">번호</th>-->
<!--        <th class="post">댓글</th>-->
<!--        <th class="subject">제목</th>-->
<!--        <th class="name">이름</th>-->
<!--        <th class="date">날짜</th>-->
<!--      </tr>-->
<!--      <tr v-for="node in topics.content" :key="node.bn">-->
<!--        <td class="seq mob-hide">{{node.bn}}</td>-->
<!--        <td class="post mob-hide">{{node.postCount}}</td>-->
<!--        <td class="subject">-->
<!--          <router-link :to="topicViewHref(node)">{{node.subject}}</router-link>-->
<!--          <div class="mob-show">댓글: {{node.postCount}} / {{node.name}} / {{node.regDt}}</div>-->
<!--        </td>-->
<!--        <td class="name mob-hide">{{node.name}}</td>-->
<!--        <td class="date mob-hide">{{node.regDt}}</td>-->
<!--      </tr>-->
<!--    </table>-->

<!--    <div v-if="isWritableTopic()" class="bt-write-wrapper">-->
<!--      <router-link :to="urlWriteTopic()" class="bt-write">글쓰기</router-link>-->
<!--    </div>-->

<!--    <pagination :href="hrefPage" :total="topics.totalPages" :index="topics.number" :unit="10" />-->

<!--  </div>-->
<!--</template>-->

<!--<script lang="ts">-->
<!--import { Component, Prop, Vue } from 'vue-property-decorator';-->
<!--import Nabi from '@/utils/nabi';-->
<!--import PageData from '@/models/PageData';-->
<!--import BoardInfo from '@/models/BoardInfo';-->
<!--import BoardTopic from '@/models/BoardTopic';-->
<!--import BoardContent from '@/models/BoardContent';-->
<!--import BoardService from '@/services/BoardService';-->
<!--import Pagination from '@/components/Pagination.vue';-->
<!--import AnissiaUtil from '@/utils/AnissiaUtil';-->
<!--import UserSession from '@/models/UserSession';-->
<!--import BoardPost from '@/models/BoardPost';-->
<!--import Md from '@/components/SaroMarkdown.vue';-->

<!--export default Vue.extend({-->
<!--  props: ['code'],-->
<!--  components: {-->
<!--    Pagination,-->
<!--    Md,-->
<!--  },-->
<!--  created() {-->
<!--    this.load();-->
<!--  },-->
<!--  watch: {-->
<!--    $route(to, from) {-->
<!--      this.load();-->
<!--    },-->
<!--  },-->
<!--  data() {-->
<!--    return {-->
<!--      info: new BoardInfo(),-->
<!--      topics: new PageData<BoardTopic>(),-->
<!--      param: '',-->
<!--      view: null as BoardContent|null,-->
<!--      md: new Md(),-->
<!--      tdata: {-->
<!--        isNewMode: false,-->
<!--        newSubject: '',-->
<!--        newContent: '',-->
<!--        newPostContent: '',-->
<!--      },-->
<!--    };-->
<!--  },-->
<!--  methods: {-->
<!--    topicViewHref(node: BoardTopic) {-->
<!--      return Nabi.address().deleteParameter('write').setParameter('view', node.bn).href;-->
<!--    },-->
<!--    load() {-->
<!--      const page: number = Number(Nabi.address().getParameter('page') || '1') - 1;-->
<!--      const view: number = Number(Nabi.address().getParameter('view'));-->
<!--      this.tdata.isNewMode = Nabi.address().getParameter('write') != null;-->
<!--      this.loadList(this.code, isNaN(page) ? 0 : page);-->
<!--      this.loadView(this.code, view);-->
<!--      this.loadInfo(this.code);-->
<!--    },-->
<!--    hrefPage(index: number): string {-->
<!--      if (index === 0) {-->
<!--        return Nabi.address().deleteParameter(['view', 'page', 'write']).href;-->
<!--      } else {-->
<!--        return Nabi.address().deleteParameter(['view', 'write']).setParameter('page', index + 1).href;-->
<!--      }-->
<!--    },-->
<!--    isWritableTopic(): boolean {-->
<!--      return this.info.isWritable(this.user());-->
<!--    },-->
<!--    isEditableTopic(): boolean {-->
<!--      const user = this.user();-->
<!--      return this.view != null && (this.view.name === user.name || user.admin);-->
<!--    },-->
<!--    isDeletableTopic(): boolean {-->
<!--      const user = this.user();-->
<!--      return this.view != null && (this.view.name === user.name || user.admin);-->
<!--    },-->
<!--    isWritablePost(): boolean {-->
<!--      return this.info.isWritablePost(this.user());-->
<!--    },-->
<!--    isEditablePost(post: BoardPost): boolean {-->
<!--      const user = this.user();-->
<!--      return post.name === user.name || user.admin;-->
<!--    },-->
<!--    isDeletablePost(post: BoardPost): boolean {-->
<!--      const user = this.user();-->
<!--      return !post.topic && (post.name === user.name || user.admin);-->
<!--    },-->
<!--    urlWriteTopic() {-->
<!--      return Nabi.address().deleteParameter('view').setParameter('write', 'new').href;-->
<!--    },-->
<!--    writeTopic() {-->
<!--      BoardService.topicWrite(this.code, this.tdata.newSubject, this.tdata.newContent, (p, m, bn) => {-->
<!--        if (p) {-->
<!--          this.param = '';-->
<!--          this.$router.push(Nabi.address().clearParameter().addParameter('view', bn).href);-->
<!--        } else {-->
<!--          alert(m || '일시적인 오류입니다.');-->
<!--        }-->
<!--      });-->
<!--    },-->
<!--    editTopic(view: BoardContent) {-->
<!--      BoardService.topicEdit(view.bn, view.subject, '-', (p, m) => {-->
<!--        if (p) {-->
<!--          this.loadView(this.code, (this.view as any).bn);-->
<!--        } else {-->
<!--          alert(m || '일시적인 오류입니다.');-->
<!--        }-->
<!--      });-->
<!--    },-->
<!--    deleteTopic() {-->
<!--      if (!confirm('정말로 삭제하시겠습니까?')) {-->
<!--        return;-->
<!--      }-->
<!--      BoardService.topicDelete((this.view as any).bn, (p, m) => {-->
<!--        if (p) {-->
<!--          this.param = '';-->
<!--          this.$router.push(Nabi.address().clearParameter().href);-->
<!--        } else {-->
<!--          alert(m || '일시적인 오류입니다.');-->
<!--        }-->
<!--      });-->
<!--    },-->
<!--    writePost(bn: number) {-->
<!--      BoardService.postWrite(bn, this.tdata.newPostContent, (p, m) => {-->
<!--        if (p) {-->
<!--          this.tdata.newPostContent = '';-->
<!--          this.param = '';-->
<!--          this.load();-->
<!--        } else {-->
<!--          alert(m || '일시적인 오류입니다.');-->
<!--        }-->
<!--      });-->
<!--    },-->
<!--    editPost(post: BoardPost) {-->
<!--      BoardService.postEdit(post.brn, post.content, (p, m) => {-->
<!--        if (p) {-->
<!--          this.loadView(this.code, (this.view as any).bn);-->
<!--        } else {-->
<!--          alert(m || '일시적인 오류입니다.');-->
<!--        }-->
<!--      });-->
<!--    },-->
<!--    deletePost(post: BoardPost) {-->
<!--      if (!confirm('정말로 삭제하시겠습니까?')) {-->
<!--        return;-->
<!--      }-->
<!--      BoardService.postDelete(post.bn, post.brn, (p, m) => {-->
<!--        if (p) {-->
<!--          this.param = '';-->
<!--          this.load();-->
<!--        } else {-->
<!--          alert(m || '일시적인 오류입니다.');-->
<!--        }-->
<!--      });-->
<!--    },-->
<!--    loadInfo(code: string) {-->
<!--      if (!this.info.exist) {-->
<!--        BoardService.info(code, (data) => this.info = data);-->
<!--      }-->
<!--    },-->
<!--    loadList(code: string, page: number) {-->
<!--      const h = JSON.stringify({code, page});-->
<!--      if (h === this.param) {-->
<!--        return;-->
<!--      }-->
<!--      this.param = h;-->
<!--      BoardService.topic(code, page, (topics) => this.topics = topics);-->
<!--    },-->
<!--    loadView(code: string, an: number) {-->
<!--      if (isNaN(an) || an <= 0) {-->
<!--        this.view = null;-->
<!--        return;-->
<!--      }-->
<!--      BoardService.view(code, an, (view) => this.view = view);-->
<!--    },-->
<!--    user(): UserSession {-->
<!--      return this.$store.state.user as UserSession;-->
<!--    },-->
<!--    render(text: string): string {-->
<!--      return this.md.render(text);-->
<!--    },-->
<!--  },-->
<!--});-->
<!--</script>-->

<!--<style>-->
<!--.bbs { padding-top:10px; }-->
<!--.bbs .board-view { padding-bottom: 50px; }-->
<!--.bbs .board-view input, -->
<!--.bbs .board-view textarea,-->
<!--.bbs .board-view .content,-->
<!--.saro-md .content { font-size:14px; }-->
<!--.bbs .board-view .not-exist-content { padding:160px 0 100px; text-align: center; line-height: 2; font-size: 20px; color:#555 }-->
<!--.bbs .board-view .board-title { line-height: 32px; margin:12px 6px 16px; }-->
<!--.bbs .board-view .board-title-edit { margin:12px 0; }-->
<!--.bbs .board-view .board-title-edit input { border:1px solid #eee; width:100%; box-sizing: border-box; height:40px; line-height: 40px; padding: 0 8px; }-->
<!--.bbs .board-view .board-title-text { font-size:18px; color:#52667b }-->
<!--.bbs .board-view .board-title-tool { float: right; text-align: right; color:#555; font-size:14px;  }-->
<!--.bbs .board-view .board-title-tool span.bt { cursor: pointer; }-->
<!--.bbs .board-view .board-title-tool span.bt:hover { color:#2c5cc3 }-->
<!--.bbs .board-view .board-title-tool span:not(:first-child):before { content: ' | '; color:#ccc }-->

<!--.bbs .board-view .write-info { overflow: auto; border: 1px solid #eee; border-radius: 4px 4px 0 0; }-->
<!--.bbs .board-view .write-info > div { line-height: 34px; padding: 0 8px; font-size:14px; }-->
<!--.bbs .board-view .write-info .write-name { float:left; color:#3758a0; }-->
<!--.bbs .board-view .write-info .write-tool { text-align: right; color:#555 }-->
<!--.bbs .board-view .write-info .write-tool span.bt { cursor: pointer; }-->
<!--.bbs .board-view .write-info .write-tool span.bt:hover { color:#2c5cc3 }-->
<!--.bbs .board-view .write-info .write-tool span:not(:first-child):before { content: ' | '; color:#ccc }-->

<!--.bbs .edit-subject { border-bottom:1px solid #ddd; }-->
<!--.bbs .edit-subject input { line-height: 40px; height:40px; padding: 0 8px; border:0; width:100%; box-sizing: border-box }-->
<!--.bbs .board-view .content { padding-bottom: 80px; }-->
<!--.bbs .board-view .content table,-->
<!--.bbs .board-view .content table td,-->
<!--.bbs .board-view .content table th,-->
<!--.saro-md .content table,-->
<!--.saro-md .content table td,-->
<!--.saro-md .content table th { border:1px solid #ddd }-->

<!--.bbs .board-topics-table { width:100%; }-->
<!--.bbs .board-topics-table tr:hover td { background: #fffffa }-->
<!--.bbs .board-topics-table th { line-height: 32px; color:#505050; font-size:12px; border-bottom: 1px solid #ccc; }-->
<!--.bbs .board-topics-table td { padding:12px 10px; color:#5b6a7f; text-align:center; font-size:14px; border-bottom: 1px solid #eee; }-->
<!--.bbs .board-topics-table td.seq { width:60px; }-->
<!--.bbs .board-topics-table td.post { width:60px; }-->
<!--.bbs .board-topics-table td.subject { padding:0 0 0 8px; text-align:left;  }-->
<!--.bbs .board-topics-table td.subject a { text-decoration: none; color:#4b5771 }-->
<!--.bbs .board-topics-table tr:hover td.subject a { color:#376298 }-->
<!--.bbs .board-topics-table td.subject a:hover { color:#2c5cc3 !important }-->
<!--.bbs .board-topics-table td.subject .mob-show { margin-top:2px; }-->
<!--.bbs .board-topics-table td.name { width:130px; color:#224f8e; }-->
<!--.bbs .board-topics-table td.date { width:130px }-->

<!--.bbs .bt-write,-->
<!--.bbs .bt-write-post { text-decoration: none; color:#333;  border:1px solid #ddd; font-size:14px; }-->
<!--.bbs .bt-write { margin:32px 4px 10px; padding:8px; display: inline-block; }-->
<!--.bbs .bt-write-post { margin:48px 0 32px; padding:8px; display:block; text-align: center }-->
<!--.bbs .bt-write-wrapper { text-align: right }-->

<!--.bbs .new-post { border-bottom: 1px solid #ddd }-->
<!--.bbs .new-post .new-post-md { border: 1px solid #eee; border-width: 0 1px; }-->

<!--@media (min-width: 700px) {-->
<!--  .mob-show { display: none }-->
<!--}-->

<!--@media (max-width: 700px) {-->
<!--  .mob-hide { display: none }-->
<!--  .bbs .board-topics-table td.subject { padding:8px 12px; line-height: 1.5 }-->
<!--  .bbs .board-topics-table td.subject a { font-size:18px; }-->
<!--}-->
<!--</style>-->