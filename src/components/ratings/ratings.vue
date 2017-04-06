<template>
	<div class="ratings" ref="ratings">
		<div class="ratings-content">
			<div class="overview">
				<div class="overview-left">
					<h1 class="score">{{seller.score}}</h1>
					<div class="title">综合评分</div>
					<div class="rank">高于周边商家{{seller.rankRate}}%</div>
				</div>
				<div class="overview-right">
					<div class="score-wrapper">
						<span class="title">服务态度</span><star :size="36" :score="seller.serviceScore"></star><span class="score">{{seller.serviceScore}}</span>
					</div>
					<div class="score-wrapper">
						<span class="title">商品评分</span><star :size="36" :score="seller.foodScore" class="star"></star><span class="score">{{seller.foodScore}}</span>
					</div>
					<div class="delivery">
						<span class="title">送达时间</span>
						<span class="delivery-time">{{seller.deliveryTime}}分钟</span>
					</div>
				</div>
			</div>
			<split></split>
			<ratingselect @select="select" @toggle="toggle" :selectType="selectType" :onlyContent="onlyContent" :ratings="ratings"></ratingselect>
			<div class="rating-wrapper">
				<ul>
					<li v-for="rating in ratings" v-show="needShow(rating.rateType,rating.text)" class="rating-item border-1px">
						<div class="avatar">
							<img :src="rating.avatar" width="28" height="28">
						</div>
						<div class="content">
							<h1 class="name">{{rating.username}}</h1>
							<div class="star-wrapper">
								<star :size="24" :score="rating.score" class="star"></star>
								<span class="delivery-time" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
							</div>
							<p class="text" v-show="rating.text">{{rating.text}}</p>
							<p class="text" v-show="!rating.text">暂无评价</p>
							<div class="recommend" v-show="rating.recommend && rating.recommend.length">
								<span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>
								<span class="item" v-for="item in rating.recommend">{{item}}</span>
							</div>
							<div class="time">{{rating.rateTime | formatDate}}</div>
						</div>
					</li>
				</ul>
			</div>
		</div>
	</div>
</template>

<script type="text/ecmascript-6">
  import BSscroll from 'better-scroll';
  import split from '../split/split';
  import star from '../star/star';
  import ratingselect from '../ratingselect/ratingselect';
  import {formatDate} from '../../common/js/formatDate.js';

  const ALL = 2;
  const ERR_OK = 0;
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        ratings: [],
        selectType: ALL,
        onlyContent: false
      };
    },
    created () {
      this.$http.get('/api/ratings').then((response) => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.ratings = response.data;
          this.$nextTick(() => {
            this.scroll = new BSscroll(this.$refs.ratings, {
              click: true
            });
          });
        }
      });
    },
    filters: {
      formatDate (time) {
        let date = new Date(time);
        return formatDate(date);
      }
    },
    methods: {
      needShow (type, text) {
        if (this.onlyContent && !text) {
          return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return type === this.selectType;
        }
      },
      select (type) {
        this.selectType = type;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      toggle () {
        this.onlyContent = !this.onlyContent;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      }
    },
    components: {
      split,
      star,
      ratingselect
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
	@import "../../common/stylus/mixsin.styl"
	.ratings
		position:absolute
		top:174px
		bottom:0
		left:0
		width:100%
		overflow:hidden
		.overview
			display:flex
			padding:18px 0
			.overview-left
				flex:0 0 137px
				width:137px
				border-right:1px solid rgba(7, 17, 27, 0.1)
				text-align:center
				font-size:0
				padding:6px 0
				@media only screen and (max-width: 320px)
					flex: 0 0 120px
					width: 120px
				.score
					font-size:24px
					color:rgb(255, 153, 0)
					line-height:28px
					margin-bottom:6px
				.title
					font-size:12px
					color:rgb(7, 17, 27)
					line-height:12px
					margin-bottom:8px
				.rank
					font-size:10px
					color:rgb(147, 153, 159)
					line-height:10px
			.overview-right
				flex:1
				padding:6px 0 6px 24px
				@media only screen and (max-width: 320px)
					padding-left:6px
				.score-wrapper
					line-height:18px
					magin-bottom:8px
					font-size:0
					.title
						font-size:12px
						color:rgb(7, 17, 27)
						vertical-align:top
					.star
						display:inline-block
						margin:0 12px
						vertical-align:top
					.score
						font-size:12px
						vertical-align:top
						color:rgb(255, 153, 0)
				.delivery
					font-size:0
					line-height:18px
					.title
						font-size:12px
						color:rgb(7, 17, 27)
						margin-right:12px
					.delivery-time
						font-size:12px
						color:rgb(147, 153, 159)
		.rating-wrapper
			padding: 0 18px
			.rating-item
				display:flex
				padding:18px 0
				border-1px(rgba(7, 17, 27, 0.1))
				.avatar
					flex: 0 0 28px
					width: 28px
					margin-right: 12px
					img
						border-radius:50%
				.content
					flex:1
					position:relative
					.name
						margin-bottom:4px
						line-height:12px
						font-size:10px
						color:rgb(7, 17, 27)
					.star-wrapper
						margin-bottom:6px
						font-size:0
						.star
							display:inline-block
							vertical-align:top
							margin-right:6px
						.delivery-time
							font-size:10px
							color:rgb(147, 153, 159)
							line-height:12px
					.text
						font-size:12px
						color:rgb(7, 17, 27)
						line-height:18px
						margin-bottom:8px
					.recommend
						line-height:16px
						font-size:0
						.icon-thumb_up, .icon-thumb_down, .item
							font-size:9px
							margin:0 8px 4px 0
							display:inline-block
						.icon-thumb_up,
							color:rgb(0, 160, 220)
						.icon-thumb_down
							color:rgb(183, 187, 191)
						.item
							font-size:9px
							color:rgb(147, 153, 159)
							border:1px solid rgba(7, 17, 27, 0.1)
							border-radius:2px
							padding:0 6px
							background:#fff
					.time
						position:absolute
						right:0
						top:0
						line-height:12px
						font-size:10px
						color:rgb(147, 153, 159)
</style>
