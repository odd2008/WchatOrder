<!--挂号列表页面-->
<template>
<div class="register-container">
    <div class="register-content">
        <van-pull-refresh v-model="isLoading" @refresh="onRefresh">
            <div class="register-item" v-for="item in registerList" v-if="!miss" :key="item.ID">

                <van-cell-group v-if="item.AM">
                    <van-cell v-bind:title="item.DATE" value="内容" v-bind:label="item.AMTIME" :to="{path:'/register',query:{'zhid':zhid,'ysbh':ysbh,'ksbh':ksbh,'id':item.ID,'type':1}}">
                        <template>
                            <span class="van-cell-text">费用：￥{{item.Amount}}</span><br>
                            <span class="van-cell-text">剩余号数：{{item.AMMOUNT}}</span>
                        </template>
                    </van-cell>
                </van-cell-group>
                <van-cell-group v-if="item.PM">
                    <van-cell v-bind:title="item.DATE" value="内容" v-bind:label="item.PMTIME" :to="{path:'/register',query:{'zhid':zhid,'ysbh':ysbh,'ksbh':ksbh,'id':item.ID,'type':1}}">
                        <template>
                            <span class="van-cell-text">费用：￥{{item.Amount}}</span><br>
                            <span class="van-cell-text">剩余号数：{{item.PMMOUNT}}</span>
                        </template>
                    </van-cell>
                </van-cell-group>

            </div>
        </van-pull-refresh>
        <div v-if="miss">
            <Miss></Miss>
        </div>
    </div>
</div>
</template>

<script>
import Miss from "../components/Miss";
import {
    PullRefresh
} from 'vant';
export default {
    name: "RegisterList",
    components: {
        Miss
    },
    data() {
        return {
            isLoading: false,
            zhid: this.$route.query.zhid,
            ksbh: this.$route.query.ksbh,
            ysbh: this.$route.query.id,
            miss: false,
            registerList: []
        };
    },
    methods: {
        /* 下拉刷新的方法 */
        onRefresh() {
            setTimeout(() => {
                this.$toast('刷新成功');
                this.isLoading = false;
            }, 500);
        },
        getRegisterList() {
            var _this = this;
            _this.$http
                .get("/api/Register/PBRQ", {
                    params: {
                        KSBH: _this.ksbh,
                        YSBH: _this.ysbh,
                        ZHID: _this.zhid,
                    }
                })
                .then(function (res) {
                    if (res.data.Ret == 0 && res.data.Data.length > 0) {
                        _this.miss = false;
                        _this.registerList = res.data.Data;
                        let tempArr = _this.registerList;
                        for (var i = 0, j = tempArr.length; i < j; i++) {
                            tempArr[i].AMTIME =
                                `上午: ${tempArr[i].AMStartTime}-${tempArr[i].AMEndTime}`;
                            tempArr[i].DATE = tempArr[i].Days.split(" ")[0];
                            tempArr[i].PMTIME =
                                `下午:  ${tempArr[i].PMStartTime}-${tempArr[i].PMEndTime}`;
                            tempArr[i].AMMOUNT = tempArr[i].AMNum;
                            tempArr[i].PMMOUNT = tempArr[i].PMNum;
                        }
                        _this.registerList = tempArr;
                    } else {
                        _this.miss = true;
                    }
                });
        }
    },
    created: function () {
        this.getRegisterList();
    }
};
</script>

<style scoped>
.register-item {
    padding: 0 10px;
}

.register-list-item {
    display: block;
    height: 100%;
    border-bottom: 1px solid #ddd;
}

.register-p {
    padding: 0.06667rem 0;
}

span.register-span {
    font-size: 16px;
    width: 3.5rem;
    padding: 0 0.26667rem;
    display: inline-block;
}

span.register-span-black {
    color: #000000;
}

span.register-span-gray {
    color: #acacac;
}

i.el-icon-arrow-right.register-list-icon {
    font-size: 20px;
    top: 0.64rem;
    right: 0.13333rem;
    position: absolute;
    color: #000;
}
</style>
