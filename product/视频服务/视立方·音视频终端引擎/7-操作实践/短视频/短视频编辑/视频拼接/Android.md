## SDK 版本
本页文档所描述功能，在 SDK 版本中支持情况如下：

| 版本名称 | 基础直播 Smart | 互动直播 Live | 短视频 UGSV | 音视频通话 TRTC | 纯播放 Player | 全功能 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- |
| 支持情况 | -  | -  | &#10003;  | -  | -  | &#10003;  |
| SDK 下载 <div style="width: 90px"/> | [下载](https://vcube.cloud.tencent.com/dev/home.html?sdk=basicLive) | [下载](https://vcube.cloud.tencent.com/dev/home.html?sdk=interactivelive) | [下载](https://vcube.cloud.tencent.com/dev/home.html?sdk=shortVideo) | [下载](https://vcube.cloud.tencent.com/dev/home.html?sdk=video) | [下载](https://vcube.cloud.tencent.com/dev/home.html?sdk=player) | [下载](https://vcube.cloud.tencent.com/dev/home.html?sdk=allPart) |

不同版本 SDK 包含的更多能力，具体请参见 [SDK 下载](https://cloud.tencent.com/document/product/1449/56978?!preview&!editLang=zh)。



## 复用现有 UI 

视频拼接器具有比较复杂的交互逻辑，这也决定了其 UI 复杂度很高，所以我们比较推荐复用 SDK 开发包中的 UI 源码。 videojoiner 目录包含短视频拼接器的 UI 源码。

![](https://main.qcloudimg.com/raw/2d8dbe98de51ac097810ea6941e415eb.png)

- TCVideoJoinerActivity 用于实现上图中的视频拼接列表，支持上下拖拽调整顺序。
- TCVideoJoinerPreviewActivity 用于预览拼接后的视频观看效果。

## 自己实现 UI
如果您不考虑复用我们开发包中的 UI 代码，决心自己实现 UI 部分，则可以参考如下的攻略进行对接：

### 1. 选择视频文件  
自己实现多选文件功能。

### 2. 设置预览 View  
视频合成需要创建 TXVideoJoiner 对象，同 TXVideoEditer 类似，预览功能也需要上层提供预览 FrameLayout：


<dx-codeblock>
::: android java
//准备预览 View        
TXVideoEditConstants.TXPreviewParam param = new TXVideoEditConstants.TXPreviewParam();
param.videoView = mVideoView;
param.renderMode = TXVideoEditConstants.PREVIEW_RENDER_MODE_FILL_EDGE;

// 创建 TXUGCJoiner 对象并设置预览 view
TXVideoJoiner mTXVideoJoiner = new TXVideoJoiner(this);
mTXVideoJoiner.setTXVideoPreviewListener(this);
mTXVideoJoiner.initWithPreview(param);
// 设置待拼接的视频文件组 mVideoSourceList，也就是第一步中选择的若干个文件
mTXVideoJoiner.setVideoPathList(mVideoSourceList);
:::
</dx-codeblock>

设置好预览 view 同时传入待合成的视频文件数组后，可以开始播放预览，合成模块提供了一组接口来做视频的播放预览：

- startPlay：表示视频播放开始。
- pausePlay：表示视频播放暂停。
- resumePlay：表示视频播放恢复。

### 3. 生成最终文件
预览效果满意后调用生成接口即可生成合成后的文件：
```
mTXVideoJoiner.setVideoJoinerListener(this);
mTXVideoJoiner.joinVideo(TXVideoEditConstants.VIDEO_COMPRESSED_540P, mVideoOutputPath);
```
合成时指定文件压缩质量和输出路径，输出的进度和结果会通过 `TXVideoJoiner.TXVideoJoinerListener` 以回调的形式通知用户。
