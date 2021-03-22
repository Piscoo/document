## video 相关

### video controls 相关控制
1. css隐藏按钮
```
//全屏按钮
video::-webkit-media-controls-fullscreen-button {
    display: none;
}
//播放按钮
video::-webkit-media-controls-play-button {
    display: none;
}
//进度条
video::-webkit-media-controls-timeline {
    display: none;
}
//观看的当前时间
video::-webkit-media-controls-current-time-display{
    display: none;            
}
//剩余时间
video::-webkit-media-controls-time-remaining-display {
    display: none;            
}
//音量按钮
video::-webkit-media-controls-mute-button {
    display: none;            
}
video::-webkit-media-controls-toggle-closed-captions-button {
    display: none;            
}
//音量的控制条
video::-webkit-media-controls-volume-slider {
    display: none;            
}
//所有控件
video::-webkit-media-controls-enclosure{ 
    display: none;
}
```
2. controlslist 隐藏按钮
```
// nodownload: 不要下载
// nofullscreen: 不要全屏
// noremoteplayback: 不要远程回放

// disablePictureInPicture: 不要画中画 

<video controls 
       disablePictureInPicture="true"
       controlslist="nodownload nofullscreen noremoteplayback"
</video>
```