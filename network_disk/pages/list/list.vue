<template>
	<view style="height: 100vh;" class="flex flex-column">
		<view class="flex border-bottom border-light-secondary" style="height: 100rpx;">
			<view
				class="flex-1 flex flex-column align-center justify-center"
				v-for="(item, index) in tabBars"
				:key="index"
				:class="index === tabIndex ? 'text-main' : ''"
				@click="changeTab(index)"
			>
				<text class="font-md">{{ item.name }}</text>
				<text style="height: 8tpx;width: 140rpx;" class="rounded" :class="tabIndex === index ? 'bg-main' : 'bg-white'"></text>
			</view>
		</view>

		<!-- swiper内容随上面tab的切换联动 -->
		<swiper :duration="250" class="flex-1 flex" :current="tabIndex" @change="changeTab($event.detail.current)">
			<swiper-item class="flex-1 flex" v-for="(item, index) in tabBars" :key="index">
				<scroll-view scroll-y="true" class="flex-1">
					<!-- 下载列表 -->
					<template v-if="index === 0">
						<view style="height: 60rpx;" class="bg-light flex align-center font-sm px-2 text-muted">文件下载至: Android/data/io.dcloud.HBuilder/apps/doc</view>
						<view class="p-2 border-bottom border-light-secondary font text-muted">下载中({{ downing.length }})</view>
						<!-- 同级还有f-list绑定了key为index，会冲突，所以加上不同的前缀区分，否则会报错 -->
						<f-list v-for="(item, index) in downing" :key="'i' + index" :item="item" :index="index">
							<view style="height: 70rpx;" class="flex align-center text-main">
								<text class="iconfont icon-zanting"></text>
								<text class="ml-1">{{ item.progress }}%</text>
							</view>
							<!-- 进度条组件，uniapp自带 -->
							<progress slot="bottom" :percent="item.progress" activeColor="#009CFF" :stroke-width="4" />
						</f-list>

						<view class="p-2 border-bottom border-light-secondary font text-muted">下载完成{{ downed.length }}</view>
						<f-list v-for="(item, index) in downed" :key="'d' + index" :item="item" :index="index" :showRight="false"></f-list>
					</template>

					<!-- 上传列表 -->
					<template v-else>
						<view class="p-2 border-bottom border-light-secondary font text-muted ">上传中{{ uploading.length }}</view>
						<f-list v-for="(item, index) in uploading" :key="'i' + index" :item="item" :index="index">
							<view style="height:70rpx" class="flex align-center text-main">
								<text class="iconfont icon-zanting"></text>
								<text class="ml-1">{{ item.progress }}</text>
							</view>
							<progress solt="bottom" :precent="item.progress" activeColor="#009CFF" :stroke-width="4" />
						</f-list>
						<view class="p-2 border-bottom border-light-secondary font text-muted">上传完成{{ uploaded.length }}</view>
						<f-list v-for="(item, index) in uploaded" :key="'d' + index" :item="item" :index="index" :showRight="false"></f-list>
					</template>
				</scroll-view>
			</swiper-item>
		</swiper>
	</view>
</template>

<script>
import fList from "@/components/common/f-list.vue";
import { mapState } from 'vuex';
export default {
	data() {
		return {
			tabIndex: 0,
			tabBars: [
				{
					name: "下载列表"
				},
				{
					name: "上传列表"
				}
			]
			// list: [
			// 	{
			// 		type: 'image',
			// 		name: '壁纸.jpg',
			// 		data: 'https://imgs.aixifan.com/o_1e09h5sut1uh39g56d1eqa1l4rv.jpg',
			// 		create_time: '2020-10-21 08:00',
			// 		download:50
			// 	},
			// 	{
			// 		type: 'image',
			// 		name: '壁纸.jpg',
			// 		data: 'https://img.3dmgame.com/uploads/images/news/20200519/1589880846_790618.jpg',
			// 		create_time: '2020-10-21 08:00',
			// 		download:100
			// 	},
			// 	{
			// 		type: 'video',
			// 		name: 'RADWIMPS1.mp4',
			// 		data: '../../static/video/RADWIMPS1.mp4',
			// 		create_time: '2020-10-21 08:00',
			// 		download:50
			// 	}
			// ]
		};
	},
	components: {
		fList
	},
	computed: {
		...mapState({
			uploadList: state => state.uploadList,
			downlist:state => state.downlist
		}),
		downing() {
			return this.downlist.filter(item => {
				return item.progress < 100;
			});
		},
		downed() {
			return this.downlist.filter(item => {
				return item.progress === 100;
			});
		},
		uploading() {
			return this.uploadList.filter(item => {
				return item.progress < 100;
			});
		},
		uploaded() {
			return this.uploadList.filter(item => {
				return item.progress === 100;
			});
		}
	},
	methods: {
		changeTab(index) {
			this.tabIndex = index;
		},
		onNavigationBarButtonTap() {
			uni.showModal({
				content:'是否要清除传输记录？',
				success:res => {
					if(res.confirm) {
						this.$store.dispatch('clearList');
						uni.showToast({
							title:'清除成功',
							icon:'none'
						});
					}
				}
			});
		}
	}
};
</script>

<style></style>
