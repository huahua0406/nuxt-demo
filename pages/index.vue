<template>
    <section class="container">
        <el-card class="box-card">
            <div :key="topic.id" class="topic-item" v-for="topic in list">
                <p class="topic-info">
                    <span class="avatar">
                        <nuxt-link :to="'/user/'+ topic.author.loginname">
                            <img :alt="topic.author.loginname" :src="topic.author.avatar_url">
                        </nuxt-link>
                    </span>
                    <span class="replay-count">
                        <span class="count_of_replies" title="回复数">{{topic.reply_count}}</span><!--
                        --><span class="count_seperator">/</span><!--
                        --><span class="count_of_visits" title="点击数">{{topic.visit_count}}</span>
                    </span>
                    <el-tag size="small" type="success" v-if="topic.top">置顶</el-tag>
                    <el-tag size="small" v-else>{{topic.tab | tabtoName}}</el-tag>
                    <nuxt-link :to="{name: 'topic-id', params: {id: topic.id}}" class="topic-title">{{topic.title}}</nuxt-link>
                    <span class="last_active_time">最后回复：{{topic.last_reply_at.replace(/T/g,' ').replace(/\.[\d]{3}Z/,'')}}</span>
                </p>
            </div>
            <el-pagination :total="1000" @current-change="handleCurrentChange" :current-page="+$route.query.page||1" background layout="prev, pager, next" style="margin-top:10px"></el-pagination>
        </el-card>
    </section>
</template>

<script>

    export default {
        watchQuery: ['page','tab'],
        data() {
            return {
                tabs: [
                    {
                        name: '全部',
                        path: 'all'
                    },
                    {
                        name: '精华',
                        path: 'good'
                    },
                    {
                        name: '问答',
                        path: 'ask'
                    },
                    {
                        name: '招聘',
                        path: 'job'
                    },
                    {
                        name: '分享',
                        path: 'share'
                    }
                ],
            }
        },
        filters: {
            tabtoName(val) {
                let mapValue = new Map()
                mapValue.set('good', '精华')
                mapValue.set('ask', '问答')
                mapValue.set('job', '招聘')
                mapValue.set('share', '分享')
                return mapValue.get(val)
            }
        },
        mounted() {
            console.log('mounted');
        },
        watch: {
            $route: function() {
                console.log('$route has changed.')
            }
        },
        head() {
            let targetTab = this.tabs.filter(item=>{
                return item.path == this.$route.query.tab
            })
            return {
                title: '首页  - ' + targetTab[0].name,
                meta: [
                    {
                        hid: 'index',
                        name: 'description',
                        content: 'CNode：Node.js专业中文社区'
                    }
                ]
            }
        },
        // asyncData方法是在组件初始化前被调用的
        // https://zh.nuxtjs.org/guide/async-data
        async asyncData({ app, store, query }) {
            console.log('asyncData start')
            console.log(store.state);
            console.log(JSON.stringify(query))
            let { data } = await app.$axios.$get(`/topics?tab=${query.tab || 'all'}&page=${query.page || 1}&limit=20`)
            return { list: data }
        },
        // https://zh.nuxtjs.org/api/pages-fetch
        async fetch({ app, store, query }) {
            // let { data } = await axios.get('http://my-api/stars')
            // store.commit('setStars', data)
        },
        methods: {
            handleCurrentChange(val) {
                window.location.href = '/?tab=' + this.$route.query.tab + '&page=' + val;
                // this.$router.push({
                //     path:'/',
                //     query:{
                //         tab:this.$route.query.tab,
                //         page:val
                //     }
                // })
            }
        }
    }
</script>

<style scoped>
    .topic-item {
        font-size: 14px;
        padding: 10px;
        border-bottom: 1px solid #f0f0f0;
    }
    .topic-info {
        display: flex;
        align-items: center;
    }
    .replay-count {
        display: inline-block;
        width: 75px;
        text-align: center;
    }
    .topic-title {
        padding: 0 5px;
        flex: 1;
    }
    .topic-title:hover {
        text-decoration: underline;
    }
    .avatar {
        display: inline-block;
        width: 30px;
        height: 30px;
        overflow: hidden;
        border-radius: 3px;
    }
    img {
        max-width: 100%;
        height: auto;
        background: #f5f5f5;
    }
</style>
