<template>
<div class="wrapper-record" >
    <van-notice-bar :scrollable="false" mode="closeable">
        点击列表，可查看个人电子病历详情。
    </van-notice-bar>
    <div class="record-content" >
        <div class="register-item" v-for="item in recordList" v-if="!miss" :key="item.NUM">
            <van-cell-group>
                <van-cell class="record-list" v-bind:title="`编号:${item.BLBH}`" value="内容" v-bind:label="`时间:${item.CJSJ}`" :to="{path:'/recorddetail',query:{'zhid':zhid,'blbh':item.BLBH,'hzbh':item.HZBH}}">
                    <template>
                        <span class="van-cell-text record-text">医生：{{item.YSXM}}</span><br>
                        <span class="van-cell-text">科室：{{item.KSMC}}</span>
                    </template>
                </van-cell>
            </van-cell-group>
        </div>
        <van-pagination v-if="showPager" v-model="currentPage" :total-items="totalItems" :show-page-size="3" force-ellipses :items-per-page="50" v-on:change="getRecordNextPage" :page-count="TotalPage" />
    </div>
    <div v-if="miss">
        <Miss></Miss>
    </div>
</div>
</template>

<script>
import Miss from "../components/Miss";
import {
    mapActions
} from 'vuex'
export default {
    components: {
        Miss
    },
    props: {},
    data() {
        return {
            showPager: false,
            miss: false,
            zhid: "",
            PageIndex: 1,
            PageSize: 50,
            recordList: [],
            currentPage: 1,
            totalItems: 30,
            TotalPage: 0
        }
    },
    watch: {},
    computed: {},
    methods: {
        ...mapActions([
            'updateState'
        ]),
        /* 获取病历信息列表 */
        getRecordList() {
            this.$http.get("/api/Register/MyBLList", {

                params: {
                    openID: this.$store.state.openid || localStorage.getItem('openid'),
                    ZHID: this.$store.state.zhid || localStorage.getItem('zhid'),
                    PageIndex: this.PageIndex,
                    PageSize: this.PageSize
                },
                headers: {
                    "X-Requested-With": "XMLHttpRequest"
                }
            }).then(res => {
                this.zhid = this.$store.state.zhid;
                let body = res.data;
                if (body.Ret == 0 && body.Data) {
                    if (body.Data.Entity) {
                        this.miss = false;
                        this.recordList = body.Data.Entity;
                        this.totalItems = body.Data.TotalCount;
                        this.TotalPage = body.Data.TotalPage;
                        if (this.TotalPage > 50) {
                            this.showPager = true;
                        }
                    } else {
                        this.miss = true;
                    }
                } else {
                    this.miss = true;
                }
            }, err => {
                this.miss = true;
            });
        },
        /* 获取下一页的方法 */
        getRecordNextPage() {
            this.PageIndex = this.currentPage;
            this.getRecordList();
        }
    },
    created() {
        this.$store.commit('updateState');
        this.getRecordList();
    },
    mounted() {}
}
</script>

<style scoped>
.record-list,
.record-list .record-text {
    font-size: 14px;
}
</style>
