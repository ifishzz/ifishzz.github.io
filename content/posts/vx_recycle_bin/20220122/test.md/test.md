# test
# 黄播app[](#黄播app)

## 基本信息[](#基本信息)

![](vx_images/5719106177152.png)  
![](vx_images/4266430189987.png)

整体架构  
app用java+后端api用tp6.0.7

```txt
http://miyudaili.com/
用户名：haha
登陆密码：123123
提现密码：123123


https://www.miyuagent.com/
https://www.miyuagent.com/agent/login/login.html


103.231.84.21
https://103.231.84.21:8888/
https://103.231.84.21:8889/

```

Copy

app里面的接口

```txt
package com.example.nmiyu.base;

public interface Urls {
    public static final String adUp = "https://www.gdfqead.com:8889/api/v1/ad_up";
    public static final String addAgent = "https://www.gdfqead.com:8889/api/login/add_agent";
    public static final String addHistory = "https://www.gdfqead.com:8889/api/v1/user/add_history";
    public static final String appConfig = "https://www.gdfqead.com:8889/api/v1/index/app_config";
    public static final String bindAccount = "https://www.gdfqead.com:8889/api/login/add_account";
    public static final String bindPhone = "https://www.gdfqead.com:8889/api/login/add_mobile";
    public static final String bookDetail = "https://www.gdfqead.com:8889/api/ent/novel_list";
    public static final String bookHome = "https://www.gdfqead.com:8889/api/ent/novel";
    public static final String bookList = "https://www.gdfqead.com:8889/api/ent/ent_list";
    public static final String cancelCollection = "https://www.gdfqead.com:8889/api/user/del_collect";
    public static final String cancelCollectionLive = "https://www.gdfqead.com:8889/api/v1/user/close_collect_live";
    public static final String cateList = "https://www.gdfqead.com:8889/api/v1/cate";
    public static final String checkCode = "https://www.gdfqead.com:8889/api/user/card";
    public static final String classifyList = "https://www.gdfqead.com:8889/api/home/zuanti";
    public static final String classifyVideoList = "https://www.gdfqead.com:8889/api/home/video_list";
    public static final String collectRankList = "https://www.gdfqead.com:8889/api/video/collect_list";
    public static final String createPayOrder = "https://www.gdfqead.com:8889/api/pay/create_order";
    public static final String doCollection = "https://www.gdfqead.com:8889/api/user/add_collect";
    public static final String doCollectionLive = "https://www.gdfqead.com:8889/api/v1/user/add_collect_live";
    public static final String dongman = "https://www.gdfqead.com:8889/api/home/dongman";
    public static final String feedback = "https://www.gdfqead.com:8889/api/user/idea";
    public static final String flyp = "https://www.gdfqead.com:8889/api/ent/loufeng";
    public static final String getCollectList = "https://www.gdfqead.com:8889/api/user/collect";
    public static final String getCollectionList = "https://www.gdfqead.com:8889/api/v1/user/collect_list";
    public static final String getContact = "https://www.gdfqead.com:8889/api/v1/service";
    public static final String getHistory = "https://www.gdfqead.com:8889/api/v1/user/hisotry_list";
    public static final String getHotVideoList = "https://www.gdfqead.com:8889/api/home/hot";
    public static final String getIp = "https://iuwtjd.com/api/login/get_ip";
    public static final String getLiveRoom = "https://www.gdfqead.com:8889/api/v1/live";
    public static final String getMissionList = "https://www.gdfqead.com:8889/api/user/tast";
    public static final String getNews = "https://www.gdfqead.com:8889/api/v1/news";
    public static final String getNotice = "https://www.gdfqead.com:8889/api/v1/notice";
    public static final String getPayList = "https://www.gdfqead.com:8889/api/pay/pay_list";
    public static final String getRanking = "https://www.gdfqead.com:8889/api/v1/ranking";
    public static final String getSearchDetail = "https://www.gdfqead.com:8889/api/home/search_list";
    public static final String getSearchResult = "https://www.gdfqead.com:8889/api/v1/search_list";
    public static final String getSms = "https://www.gdfqead.com:8889/api/user/send_code";
    public static final String getTaskGift = "https://www.gdfqead.com:8889/api/user/draw_tast";
    public static final String getThemeList = "https://www.gdfqead.com:8889/api/v1/theme";
    public static final String getUserInfo = "https://www.gdfqead.com:8889/api/v1/user/user_info";
    public static final String getVideoDetail = "https://www.gdfqead.com:8889/api/v1/video/video_info";
    public static final String getVideoList = "https://www.gdfqead.com:8889/api/v1/video/list";
    public static final String getVipList = "https://www.gdfqead.com:8889/api/v1/meal";
    public static final String guochan = "https://www.gdfqead.com:8889/api/home/guocan";
    public static final String homeData = "https://www.gdfqead.com:8889/api/home/index";
    public static final String host = "https://www.gdfqead.com:8889/";
    public static final String host_ip = "https://iuwtjd.com/";
    public static final String hotRankList = "https://www.gdfqead.com:8889/api/video/hot_list";
    public static final String liveAnchor = "https://www.gdfqead.com:8889/api/v1/live_anchor";
    public static final String liveCate = "https://www.gdfqead.com:8889/api/live/cate";
    public static final String liveCollectionList = "https://www.gdfqead.com:8889/api/v1/user/collect_live_list";
    public static final String liveIndex = "https://www.gdfqead.com:8889/api/live/index";
    public static final String liveInfo = "https://www.gdfqead.com:8889/api/live/info";
    public static final String login = "https://www.gdfqead.com:8889/api/Login/login";
    public static final String loginWithAccount = "https://www.gdfqead.com:8889/api/login/account_login";
    public static final String loginWithMobile = "https://www.gdfqead.com:8889/api/login/mobile_login";
    public static final String loginWithScan = "https://www.gdfqead.com:8889/api/login/scan";
    public static final String mangaHome = "https://www.gdfqead.com:8889/api/ent/comic";
    public static final String mangaHomeDetail = "https://www.gdfqead.com:8889/api/ent/comic_list";
    public static final String musicHome = "https://www.gdfqead.com:8889/api/ent/audio_novel";
    public static final String musicHomeDetail = "https://www.gdfqead.com:8889/api/ent/audio_novel_list";
    public static final String newRankList = "https://www.gdfqead.com:8889/api/video/news_list";
    public static final String oumei = "https://www.gdfqead.com:8889/api/home/oumei";
    public static final String payDetail = "https://www.gdfqead.com:8889/api/pay/meal";
    public static final String playRankList = "https://www.gdfqead.com:8889/api/video/view_list";
    public static final String rankDetail = "https://www.gdfqead.com:8889/api/video/rank";
    public static final String rankHot = "https://www.gdfqead.com:8889/api/video/hot_list";
    public static final String rankLook = "https://www.gdfqead.com:8889/api/video/view_list";
    public static final String rankNew = "https://www.gdfqead.com:8889/api/video/news_list";
    public static final String rankSc = "https://www.gdfqead.com:8889/api/video/collect_list";
    public static final String removeHistory = "https://www.gdfqead.com:8889/api/v1/user/close_history";
    public static final String rihan = "https://www.gdfqead.com:8889/api/home/rihan";
    public static final String searchBottomVideo = "https://www.gdfqead.com:8889/api/home/serach_recommend";
    public static final String searchDetail = "https://www.gdfqead.com:8889/api/home/search";
    public static final String signName = "Miyu";
    public static final String tagSearch = "https://www.gdfqead.com:8889/api/home/tag_search";
    public static final String testToken = "https://www.gdfqead.com:8889/api/login/shixiao";
    public static final String upTest = "https://www.gdfqead.com:8889/api/login/ceshi";
    public static final String updateApp = "https://www.gdfqead.com:8889/api/v1/version";
    public static final String userInfo = "https://www.gdfqead.com:8889/api/user/info";
    public static final String videoChange = "https://www.gdfqead.com:8889/api/home/video_huan";
    public static final String videoFeedback = "https://www.gdfqead.com:8889/api/video/wrong";
    public static final String videoInfo = "https://www.gdfqead.com:8889/api/home/video_info";
}
```

Copy

app内交互非常少，客服使用livechat，后台的特征也很少

后续就是找源码，对用户后台的测试，找管理后台