首先，此代码需要用HBuilderX打开，工具下载地址：[下载地址](https://www.dcloud.io/hbuilderx.html)
其次，此语言为uniapp 虽然性能不是很好，但是做个追剧周表还是可以的
主要代码在pages/index/index.vue内，可自行修改

需要修改四处地方
第一处在App.vue globalData里面的四个变量
第二处在uni.scss 这里有个变量是$blbl 可以修改为自己喜欢的主题色
第三处在manifest.json 基础配置里需要是你自己的APPID，如果没用点击重新获取即可；
	选择Web配置 里面有个 运行的基础路径，这个是你网站存放在服务器的地址，

修改完之后就可以点击工具上方 发布 -> 网站-PC -> 然后发布就行了（可能需要登录、实名认证什么的）

第四处在 cms源码里面 addons/apptov3/app/api/controller/Aapi.php 如果没有这个文件请在群内下载
在里面正确的位置加上下方代码即可，目前只能实现按星期查询（如果已经添加了就不需要添加了）

	public function dateList(){
        $weekday = $this->request->get('weekday');
        $pg = $this->request->get('pg');
        $where['vod_weekday'] = ['like', '%'.$weekday.'%'];
        $order = 'vod_time desc';
        $field = 'vod_id,vod_remarks,vod_name,vod_pic,vod_blurb,vod_weekday';
        $info = (new Vod())->listData($where, $order, $pg, 20, 0, $field, 0);
        echo json_encode($info, 456);
    }
	
	
 - BY易仝 1944876825