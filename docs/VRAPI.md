### VR API 接口文档 

#### 开发流程

开发分为五个步骤，
1、创建DOM渲染容器提供给webGL渲染显示场景

    <div id='example'></div>

2、获取渲染容器，初始化渲染器，绑定到该容器

    container=document.getElementById('example')
    renderer = new THREE.WebGLRenderer();
    container.appendChild(renderer.domElement);

3、初始化3D场景

    scene = new THREE.Scene();

4、将场景、容器和渲染器绑定到VR播放器，以及播放器设置视角FOV设置

    /**
    * 播放器初始化
    * @param scene threejs场景对象
    * @param renderer 渲染器对象
    * @param container 播放器容器dom对象
    * @param cameraPara {
                "fov": 90,
                "aspect": that.container.innerWidth / that.container.innerHeight,
                "near": 0.001,
                "far": 1000
            }
    * @param cameraPosition {
                "x": 0,
                "y": 0,
                "z": 0
            }
    */
    var vr=new VR(scene,renderer,container,{"fov":50});

    // 直播设置
    vr.liveSettings = {
        "forceUseHls": false,
        "forceUseVndAppleMpegUrl": false,
        "forceUseXmpegUrl": false,
        "usePlugin": false,
        "loadPlugin": function (video) {
            console.log('load video', video);
        }
    }
    // 设置hls.js 初始化参数
    vr.hlsConfig = {
        autoStartLoad: true,
    };
    // 设置flv.js 初始化参数
    vr.flvConfig = {
        type: 'flv',
        isLive: true,
    };

    // 设置工具栏自动隐藏时间
    this.defaultAutoHideLeftTime = 3;
    // 设置声音控制自动隐藏时间
    this.defaultVoiceHideLeftTime = 2;
    // 设置默认音量大小，pc和部分android有效
    this.defaultVolume = 0.3;

    // 播放初始配置
    vr.playCfg = {
        muted: false,
        autoplay: false,
    };

    // 播放事件触发回调
    vr.videoPlayHook = function () {
        console.log('video play')
    }
    // 暂停事件触发回调
    vr.videoPauseHook = function () {
        console.log('video pause')
    }

    vr.init()

5、播放VR

    /**
    * 播放视频
    * @param url 播放地址
    * @param resType VR播放类别：
        vr.resType.video 播放VR视频
        vr.resType.box 天空盒子模式
        vr.resType.slice 全景图片切片模式
        vr.resType.sliceVideo 全景视频分片模式或者是HLS直播模式
        vr.resType.flvVideo FLV直播模式
    * @praram options 该参数会覆盖vr.playCfg参数配置
    */
    vr.playPanorama(url,resType,options);
    vr.play(url,resType,options)


#### 资源加载精度回调

    在全景播放前面定义资源加载状态回调方法：
    //全景资源加载完成回调
    vr.loadProgressManager.onLoad(xhr){
        console.log("loaded");
    }
    //全景资源加载中回调
    vr.loadProgressManager.onProgress(item, loaded, total){
        console.log("item=",item,"loaded",loaded,"total=",total);
    }
    //全景资源加载错误回调
    vr.loadProgressManager.onError(xhr,cl) {
        console.log(xhr,cl);
    }

#### 场景对象

    获取当前场景，兼容threejs操作
    vr.scene;
    
    获取当前渲染器对象，兼容threejs操作
    vr.renderer;
    
    获取播当前播放器容器dom对象
    vr.container;
    
    获取视频对象，该对象支持video标签的所有事件和方法
    vr.video;
    // 例如
    vr.video.pause()/暂停视频
    vr.video.play()/播放视频
    vr.video.addEventListener('loadedmetadata', function () {
        // code ...
    });
    
    获取音频对象,该对象支持audio标签所有事件和方法，主要用于独立控制和播放音频
    vr.audio;
    // 例如
    vr.audio.paush();//暂停音频
    vr.audio.play();//播放音频

    // 获取播放器状态栏所有可操作的元素的dom对象，如获取时间显示区域对象可用vr.toolBar.timeInfo即可获取
    vr.toolBar
    
#### 自动旋转

    设置播放器镜头自动旋转
    vr.controls.autoRotate=true

    设置自动旋转速度为1.2
    vr.controls.autoRotateSpeed=1.2

#### 陀螺仪

    关闭陀螺仪
    vr.controls.gyroFreeze()

    开启陀螺仪
    vr.controls.gyroUnfreeze()

#### 截屏

    vr.takeScreenShot(function(screenshotImg){})

#### 全景图切片

    全景图切片大小设置
    vr.sliceSegment=0; 
    如果全景图切片的话需要指定，最终切片数量=sliceSegment*sliceSegment*6 片

    全景图切片,width,height 越大，切出来的图片空间占用越大，清晰度也越好，（注意，不建议设置的太大，切片需要占用大量的内存，部分浏览器会崩溃）
    vr.sphere2BoxPano(img, width, height,function (imgArray) {})
    
#### 小行星视角初始化参数

    不启用小行星视角
    vr.asteroidConfig.enable=false

    视角移动速度，值越小，移动越快 ms
    vr.asteroidConfig.assteroidFPS=36

    俯视视角大小
    vr.asteroidConfig.assteroidFov=135

    小行星视角到正常视角更新完成总耗时 ms
    vr.asteroidConfig.asteroidForwardTime=2000

    小行星开始之前等待时间 ms
    vr.asteroidConfig.asteroidWaitTime=1000

    vr.asteroidConfig.asteroidDepressionRate=0.5
    
    小行星视角方向[1 俯视/-1 仰视]
    vr.asteroidConfig.asteroidTop=1
    
    立体相机宽度
    vr.asteroidConfig.cubeResolution=2048

### 通用组件对象使用说明

    //获取播放器使用的svg图标集合
    AVR.playerIcon
    // 例如可以获取播放按钮的svg图像AVR.playerIcon.playSvg,该图片可以使用AVR.playerIcon.playSvg="..."替换成自己需要的图标，前提是需要放在播放器初始化之前设置好
    // 播放图标：AVR.playerIcon.playSvg
    // 暂停图标：AVR.playerIcon.pauseSvg
    // 回正视角图标：AVR.playerIcon.resetLookAtSvg
    // 陀螺仪图标：AVR.playerIcon.gyroSvg
    // VR图标：AVR.playerIcon.vrSvg
    // 播放音频图标：AVR.playerIcon.audioPlaySvg
    // 暂停音频图标：AVR.playerIcon.audioPauseSvg

    /**
    * 获取摄像头对象空间方向矢量
    * @param camera 当前vr摄像头对象
    * @param times 单位方向矢量缩放倍数
    */
    AVR.cameraVector: function (camera, times)

    /**
    * 捕获涉嫌穿过的模型集合
    * @param event 鼠标事件对象e
    * @param vr 当前vr对象
    * @param callback 回调方法对象{success:funciton(){},empty:funciton(){}}
    */
    AVR.bindRaycaster: function (event, vr, callback)

    /**
    * 绑定camera触发事件，用于VR模式通过场景内的摄像头焦点触发用户操作
    * @param vr 当前vr对象
    * @param options {
                vectorRadius: vr.vrbox.width / 2.2,
                trigger: function (e) {
                    console.log("on", e.jsonInfo)
                },
                move: function (e) {
                    intersectObj = e[0].object;
                    console.log('intersectObj', intersectObj)
                },
                empty: function () {
                    vr.cameraEvt.leave();
                }
            }
    */
    AVR.bindCameraEvent:function (vr, options)

    /**
    * 屏幕坐标转3D场景坐标
    * @param e 鼠标事件e
    * @param container 播放器对象
    * @param camera vr摄像头对象
    */
    AVR.screenPosTo3DCoordinate: function (e, container, camera)

    /**
    * 3D场景坐标转屏幕坐标
    * @param object 3D场景内的模型对象
    * @param container 播放器container对象
    * @param camera 当前vr摄像头对象
    */
    AVR.objectPosToScreenPos: function (object, container, camera)

    /**
    * 判断是否为移动设备
    */
    AVR.isMobileDevice()

    /**
    * 判断是否为横屏模式
    */
    AVR.isCrossScreen()

    AVR.OS.isGooglePixel();
    AVR.OS.isMiOS();
    AVR.OS.isSamsung();
    AVR.OS.isMobile();
    AVR.OS.isAndroid();
    AVR.OS.isiOS();
    AVR.OS.isWeixin()
    
    AVR.Broswer.isIE();
    AVR.Broswer.ieVersion();
    AVR.Broswer.isEdge();
    AVR.Broswer.isSafari();
    AVR.Broswer.is360();
    AVR.Broswer.isSogou();
    AVR.Broswer.isChromium();
    AVR.Broswer.webglAvailable();
