# **Python NetEase Music Playlist Helper**

# 功能说明

Github 有许多 NeteaseMusic 的脚本或其他书写的 API 调用，其全部侧重点在高质量音乐下载、网易云官方歌曲排行榜歌单收听等。

此脚本的侧重点与他们全部不同。

此脚本不包含歌曲下载、收听，定位于帮助对歌单有强迫症的用户整理歌单，如：

1、歌单中的歌曲批量排序，调整位置;

2、批量新建歌单;

3、歌单之间的拷贝;

4、大量歌曲批量归类到以音乐人命名的歌单;

5、建立 html 特殊字符名字的歌单，如名字包含 @ # 。


# 使用

### 用户登录方式

使用浏览器的 Cookies ，因此使用脚本前先确保曾经使用 Chrome 网页登陆成功过网易云。

暂不支持手机（cellphone）、邮箱（mail）的登陆方式。

使用 Cookies 的方式可用性强。

笔者自己的账户就是通过微博登陆网易云，未有手机绑定，也就不存在手机号登陆的方式，此方式无效，只能使用 Cookies。

### 支持的 NeteaseAPI 调用

见 `class NeteaseMusic`

base_url = 'http://music.163.com/weapi'

/user/playlist 获取用户所有歌单

/v3/playlist/detail 指定歌单内的所有音乐

/playlist/manipulate/tracks 维护指定歌单，添加删除音乐

/playlist/create 新建歌单

/playlist/delete 删除歌单

在以上 API 的基础上进行组装，实现更高级的需求，如歌单内音乐排序。

### 歌单、音乐在网易云中的标识

歌单、音乐都通过 id 来标识，通过网页可轻易查看：

歌单 id ：浏览器地址栏，如 http://music.163.com/#/playlist?id=748829802 这样的地址，id 后的数字即为此歌单的 id

音乐 id ：浏览器地址栏，如 http://music.163.com/#/song?id=446512356 这样的地址，id 后的数字即为此歌曲的 id

id 为通过网易云 API 访问、修改歌单数据的凭证。

##### `io_in_out.py` in `netease.py`

见 `https://github.com/fooofei/py_pieces/blob/master/io_in_out.py`

# 实现需求

### 歌单内歌曲排序

未发现网易云 API 对歌单排序的功能，且排序可用的客户端有 Windows 客户端、Android 手机端、iOS 手机端可用，
不可用的有 Mac 端，Web 端。曲折实现对音乐排序对需求，如下，

如对已存在歌单 a 进行音乐排序，实现过程：

1 获取歌单 a 中的所有音乐，此过程通过网易云 API 进行实现；

2 将 a 中的所有音乐按照需求进行排序，此过程不访问网易云 API，不对歌单做修改，在本地实现；

3 新建临时歌单 b，将步骤 2 中的歌曲全部插入，此过程用到网易云 API，会为你新建一个歌单；

4 清空歌单 a 中的音乐，此过程用到网易云 API，会把原来的歌单清空，不要担心丢失音乐，在上一个步骤，我们已经做了备份；

5 将歌单 b 中的音乐全部插入歌单 a，此过程用到网易云 API，对歌单 a 插入数据；

6 删除备份歌单 b，歌单 a 已经如我们的需求完成了排序。

以上过程都通过脚本工具完成，任何一个过程的失败，都不会丢失歌单数据。

此实现方式保留了原有歌单，原有歌单的播放次数，订阅人都会保留。


### 多个歌单之间音乐去重复


如有大歌单 A ，集结音乐 500 首，还有小歌单 b,c,d ，可以实现从大歌单 A 中删除小歌单 b,c,d 中已经有的音乐。
实现较为简单，获取所有歌单中的音乐列表，在本地处理，找到重复的音乐，然后将重复的音乐从大歌单 A 中删除即可。
