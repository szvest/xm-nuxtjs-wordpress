<template>
  <div class="container">
    <ul class="header">
      <li class="list">当前频道：{{ $route.query.title }}</li>
    </ul>
    <div v-if="articleList.length === 0" class="not">暂无数据！</div>
    <article v-else class="article-list" v-for="item in articleList" :key="item.key">
      <nuxt-link :to="{ name: 'details-id', params: { id: item.id } }" class="thumbnail-wrap">
        <img :src="item.articleInfor.thumbnail === null ? $store.state.info.setExtend.thumbnail : item.articleInfor.thumbnail.replace(/https?:\/\/.+\:\d+/, '')" class="thumbnail" alt="">
      </nuxt-link>
      <div class="list-content">
        <h2 class="title">
          <nuxt-link :to="{ name: 'details-id', params: { id: item.id } }" v-html="item.title.rendered"></nuxt-link>
        </h2>
        <p class="summary">{{ item.articleInfor.summary }}</p>
        <div class="opeartion">
          <div class="information">
            <span><i class="iconfont icon-time"></i>{{ item.date.replace('T', ' ') }}</span>
            <span><i class="iconfont icon-eye"></i>{{ item.articleInfor.viewCount }}</span>
            <span><i class="iconfont icon-message"></i>{{ item.articleInfor.commentCount }}</span>
            <span><i class="iconfont icon-zan"></i>{{ item.articleInfor.xmLike.very_good }}</span>
          </div>
          <nuxt-link class="details-btn" :to="{ name: 'details-id', params: { id: item.id } }">阅读详情</nuxt-link>
        </div>
      </div>
    </article>
    <!-- more btn start -->
    <el-pagination
      :page-size="8"
      layout="prev, pager, next, jumper"
      :current-page.sync="nCurrentPage"
      @current-change="currentPage"
      @prev-click="prevPage"
      @next-click="nextPage"
      :total="total">
    </el-pagination>
    <!-- more btn end -->
  </div>
</template>

<script>
import axios from '~/plugins/axios'
export default {
  watchQuery: ['type'],
  async asyncData ({ params, query }) {
    let [list] = await Promise.all([
      axios.get(`${process.env.baseUrl}/wp-json/wp/v2/posts`, {
        params: {
          categories: query.type,
          page: params.id,
          per_page: 8,
          _embed: true
        }
      })
    ])
    return {
      articleList: list.data,
      total: +list.headers['x-wp-total'],
      nCurrentPage: +params.id
    }
  },
  name: 'Category',
  head () {
    return {
      title: `${this.$route.query.title} | ${this.$store.state.info.blogName}`
    }
  },
  methods: {
    currentPage (n) {
      this.$router.push({
        name: 'category-id',
        params: {
          id: n
        },
        query: {
          type: this.$route.query.type,
          title: this.$route.query.title
        }
      })
    },

    // 上一页
    prevPage (n) {
      this.$router.push({
        name: 'category-id',
        params: {
          id: n
        },
        query: {
          type: this.$route.query.type,
          title: this.$route.query.title
        }
      })
    },

    // 下一页
    nextPage (n) {
      this.$router.push({
        name: 'category-id',
        params: {
          id: n
        },
        query: {
          type: this.$route.query.type,
          title: this.$route.query.title
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped>
// 文章列表
.container{
  padding: $container-padding;
  background: $color-white;
  border-radius: $border-radius;

  .header{
    padding-bottom: $container-padding;
    border-bottom: 1px solid $color-main-background;
    font-size: $font-size-large;
  }

  // 暂无数据
  .not{
    margin: 15px 0;
    text-align: center;
    color: $color-theme;
  }
}

@media screen and (max-width:767px) {
  // 文章列表
  .container{
    .article-list{
      flex-wrap: wrap;
      height: auto;

      .title{
        margin-top: 15px;
        font-size: $font-size-large;
      }

      .summary{
        height: auto;
      }

      .list-content{
        height: auto;
      }

      .opeartion{
        position: static;
        display: block;
        margin-top: 10px;
      }

      .details-btn{
        display: block;
        margin-top: 10px;
        padding: 10px 0;
        text-align: center;
      }
    }

    .thumbnail-wrap{
      width: 100%;
      margin-right: 0;
      text-align: center;

      .thumbnail{
        width: auto;
        height: auto;
        max-height: 150px;
      }
    }
  }

  // 翻页
  /deep/ .el-pagination{
    .el-pagination__jump{
      display: none;
    }
  }
}
</style>
