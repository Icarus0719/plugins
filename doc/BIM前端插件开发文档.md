# BIM前端JS插件API开发文档

## 概述
BIM前端JS插件是一套基于threejs的三维可视化应用接口，满足基本的三维展示，渲染的功能，同时也提供了三维模型的增删改查功能，模型添加标签功能，设备定位功能等服务接口。开发者不需要再重新书写threejs的基本代码，就能够在开发中实现三维模型的展示，当然由于三维场景定制化太多，本插件并不能完全覆盖所有threejs功能，开发者依旧需要了解threejs，才能更好的定制化开发。

## 教程

### 准备

注册账号获取key和mapId

        1、开发者需在BIM管理平台注册，获取key;
        2、开发者在该key下，上传模型，获取场景的mapId;
准备页面

        1、在页面中引入Bim.umd.js脚本
        2、添加div标签作为三维场景容器，容器需指定宽度和高度

### 快速入门
准备工作完成之后就可以真正开始三维场景的开发工作。
+ 1、创建三维场景只需要一行代码，创建构造函数，传入容器dom节点即可：

        const dom = document.getElementById("webgl");
        const bim = new Bim.default(dom);
+ 2、根据key和mapId，调用后台查询接口"/sceneQuery"获取场景下的树形结构数据，调用importModelByDatas()方法即可在页面中渲染上传的模型：
        
        //传参data为数组类型 
        bim.importModelByDatas(data)

### 属性

#### .CSS2DLabel
threejs中的CSS2D标签对象，由<a href='#add2d'>addCSS2DLabel()</a>方法生成
#### .allObjectsInScene
场景中所有的模型集合
#### .camera
场景中的相机对象，同<a href='#camera'>.initScene.camera</a>
#### .newObjects
场景中的新增模型时的临时合集，因为只有安装成功后，才会加入allObjectsInScene中
#### .orbitControls
同<a href='#orbit'>.initScene.orbitControls</a>
#### .transformControls
同<a href='#trans'>.initScene.transformControls</a>
#### .outlinePass
同<a href='#outline'>.initScene.outlinePass</a>
#### .scene
同<a href='#scene'>.initScene.scene</a>
#### .selectedModel
选中的模型对象

#### .initScene 
场景信息对象，包存场景的基本信息：
#####  .initScene.animate
场景中的循环渲染函数
#####  .initScene.animationId
浏览器自定义的循环函数id，监听渲染函数，主要用于离开页面清除渲染函数
#####  .initScene.camera
<a name="camera"></a>
threejs中相机对象，默认是透视相机，详见
https://threejs.org/docs/index.html#api/zh/cameras/PerspectiveCamera
#####  .initScene.CSS2DLabelRenderer
threejs中CSS2D标签渲染器，详见
https://threejs.org/docs/index.html#examples/zh/renderers/CSS2DRenderer
#####  .initScene.CSS3DLabelRenderer
threejs中CSS3D标签渲染器，详见
https://threejs.org/docs/index.html#examples/zh/renderers/CSS3DRenderer
#####  .initScene.cancelAnimationFrame()
取消threejs中的循环渲染函数，用于离开页面时调用，防止场景一直消耗性能
#####  .initScene.clock
threejs中Clock对象，用于追踪时间，详见：
https://threejs.org/docs/index.html#api/zh/core/Clock
#####  .initScene.composer
threejs中的效果组合器对象，用于后期处理通道，详见：
https://github.com/mrdoob/three.js/blob/master/examples/js/postprocessing/EffectComposer.js
#####  .initScene.FXAAShader
threejs中抗锯齿着色器
#####  .initScene.el
容器Dom标签
#####  .initScene.lights
场景中灯光集合
#####  .initScene.mixer
threejs中的动画混合器，详见
https://threejs.org/docs/index.html#api/zh/animation/AnimationMixer
#####  .initScene.orbitControls
<a name="orbit"></a>
threejs中的轨道控制器，用于漫游场景，详见
https://threejs.org/docs/index.html#examples/zh/controls/OrbitControls
#####  .initScene.outlinePass
<a name="outline"></a>
threejs中的轮廓处理通道，详见
https://github.com/mrdoob/three.js/blob/master/examples/js/postprocessing/OutlinePass.js
#####  .initScene.scene
<a name="scene"></a>
threejs中场景对象，详见https://threejs.org/docs/index.html#api/zh/scenes/Scene
#####  .initScene.setAnimationClips()
播放模型中带有动画特性的模型
#####  .initScene.stats
JavaScript性能监控器，用于监控三维场景的渲染性能
#####  .initScene.transformControls
<a name="trans"></a>
threejs中的变换控制器，用于模型在场景中进行移动，旋转，缩放等变换
#####  .initScene.webGLRenderer
threejs中的webGLRenderer渲染器，详见
https://threejs.org/docs/index.html#api/zh/renderers/WebGLRenderer

### 方法
#### .addCSS2DLabel(dom)
<a name="add2d">函数：</a>场景中生成CSS2D标签，参数为需要展示的容器DOM标签

#### .deleteCSS3DDom(model)
删除模型上的css3Dlabel的DOM节点,参数为模型对象,类型为Object
#### .fixedNewModel(event)
鼠标操作，场景中安装新设备的函数，参数为鼠标事件对象，返回结果对象，如：
        const res = selectModelFunc(event);
        console.log(res)
        
        res={
            result:"",//安装结果信息
            currentModel:model, //操作的新模型对象
            intersects:[] //鼠标事件与三维场景相交的所有模型信息
            }
        

#### .getModelInFloor(floorNode)
获取场景中楼层节点下的所有模型，包括本身模型和里面的设备模型，参数为节点对象，类型为Object，函数返回的是一个对象，结构如下：

        {
            floorModel, //当前楼层模型
            equipModels, //所有设备模型
            allModelInFloor //模型合集
        }
#### .getModelInScene(param)
获取场景中的单个模型，传参为{key:value}形式，可根据模型的id,name，imei进行获取，函数返回的是一个数组，如：

       const model = bim.getModelInScene({ name: "p_man" });
       const model = bim.getModelInScene({ id: 1 });
       const model = bim.getModelInScene({ imei: "123456" });
       console.log(model) //[model]

#### .getModelsAsFId0(nodes)
获取场景中所有floorId=0的设备模型，floorId为0表示模型是在室外，参数为模型信息的节点合集，类型为Array，返回结果是模型的数组合集，如：

        const nodes = [{floorId:0,name:"烟感"},{floorId:0,name:"摄像头"}];
        const res = bim.getModelsAsFId0(nodes);
        console.log(res) //[modelA,modelB]

#### .importModelByDatas(node,deep)
场景中加载模型函数，参数node，为从后台拿到的树形结构数据合集，类型是Array，参数deep，选填，表示是否深度加载节点模型，true是加载所有子模型，false表示只加载当前第一级模型，默认false, 如：

        const nodes=[
            {
                buildId: 2,
                children: [
                    {
                        buildId: 2,
                        children: [],
                        defines: ""
                        floorId: 18,
                        format: ".fbx",
                        isload: 0,
                        isquery: 0,
                        mapId: 1,
                        name: "17F",
                        position: null,
                        proTypeId: 1100000,
                        tablename: "map_build_info",
                        url: "/models/FBX/17F_A.FBX,↵/models/FBX/17F_B.FBX,↵/models/FBX/17F_C.FBX"
                    }
                ],
                defines: "{a:1}",
                format: ".fbx",
                isload: 1,
                isquery: 0,
                mapId: 1,
                name: "腾飞大厦C座",
                position: {},
                proTypeId: 1,
                tablename: "map_build_info",
                url: "/models/gltf/yuanqu.glb"}
            ]
        
        bim.importModelByDatas(nodes) //只加载 腾飞大厦C座 的模型
        bim.importModelByDatas(nodes,true) //全部模型都加载，包括17F的模型

#### .importModelIfNew(data)
加载新增模型方法，参数为新模型的基本信息对象，必须包含模型的url字段，该信息是从模型库接口获取的，如：

        const data={
            "format":".fbx",
			"groupName":"设备资源",
			"id":5,
			"key":"0",
			"name":"NB-烟感",
			"proTypeId":100105,
			"tableName":"map_sensor",
			"thumbUrl":"/static/imgs/nb_smoke.png",
			"url":"/models/FBX/modelLab/smoke.FBX"
        }
        bim.importModelIfNew(data)
#### .lookAtModel(position)
相机定位模型的方法，参数为所要定位模型的position信息
#### .onMouseMove()
场景中的鼠标滑动事件，新增模型时，展示模型跟随鼠标的功能，未新增时，显示鼠标接触到的设备模型的外轮廓功能
#### .removeModel(model)
删除场景中的某个模型，参数为需要是需要删除的模型对象
#### .resetCamera(data)
重置相机参数，包括相机的position,near,far参数，传参为相关参数对象，如：

        const data={
            position:{
                x:1,
                y:2,
                z:10
            },
            near:0.1,
            far:10000
        }
        bim.resetCamera(data)
#### .selectModelFunc(event)
通过鼠标事件选中模型，包括单击，双击，滑动等鼠标事件，参数是事件对象，返回选中的模型对象
#### .setModelSpace(group, modelData)
设置模型在场景中的信息，主要是模型的位置position和旋转rotate信息，参数1是需要设置的模型，注意此处传入的model必须是group,不能是mesh，因为一个模型可能包括多个mesh和标签等其他Object，他们是以组合在一起的，要移动或旋转必须是整体，参数2是重新设置的空间信息对象
#### .setTransformMode(model, type)
设置模型上的变换控制器类型。参数1为选中的模型，参数2是变换控制器的类型，可选值为"translate"---平移，"rotate"---旋转，"scale"---缩放
#### .showOrHideCSS2DLabel(visible,model)
显示或隐藏CSS2D标签函数，visible为必填，为Boolean，model是需要被隐藏标签的模型，默认是当前模型，即bim.selectedModel
#### .showOrHideModels(model, visible)
显示或隐藏场景中全部或部分模型，参数1为需要操作的模型数组，参数2为Boolean