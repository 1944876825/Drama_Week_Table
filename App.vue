<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		},
		globalData: {
			cms: 'http://43.249.8.162:37264/',  // cms域名
			Scheme: 'lefansj',  // appto里的 Custom Scheme
			Host: 'app'  ,// appto里的 Custom Host
			defineImg: 'https://appto-v3-1251970844.cos.ap-chongqing.myqcloud.com/theme/default/loading.gif',  // 影片照片加载失败默认图片
		}
	}
</script>

<style>
	/*每个页面公共css */
	html,
	body {
		padding: 0;
		margin: 0;
	}
</style>
