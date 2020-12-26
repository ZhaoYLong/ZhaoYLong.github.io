---
layout:     post
title:      Vue2和Echarts的奇妙反应

subtitle:   基于项目实战的全覆盖讲解
date:       2020-12-26
header-img: img/post-bg-debug.png
catalog:    false
tag:
    - Vue
    - Echarts
    - 前端
---

> 为什么要写这篇博客呢，就是想把最近2个月使用Echarts的心得记录一下。我在10月入职了一家做地质方面的大数据公司，前端开发工作中主要使用到的技术栈：Vue+Leaflet+Supmap iClient+Echarts，后面项目可能会用到three.js但今天要讲的是Echarts的使用心得。

> Echarts的前世今生我就不介绍了，感兴趣的可以去官网一探究竟。前端若是要开发图表功能，大概率都是使用Echarts或基于Echarts封装的工具。

> [Vue3官网](https://vue3js.cn/)，我目前使用的是Vue2！[Vue2🔗](https://vuejs.org/)

> [Echarts官网](https://echarts.apache.org/en/index.html)

> [Echarts相关的API和配置项](https://echarts.apache.org/en/api.html#echarts)

## 1 创建Vue项目并引入Echarts

### 1.1 使用`Vue-Cli`脚手架创建项目
```shell
    vue create vue-echarts
```
- 创建过程中会选择一些需要提前引入的包比如`ESlint`、`Vue-route`等。

- 创建完项目后使用`npm`命令来安装所需要的Echarts包：
  ```shell
    npm install echarts --save
  ```

  ![创建项目](/img/vue-echarts/vue-create.png)

- 也可以之间在`package.json`里的`dependencies`中直接写入Echarts包的版本，如下，

  - 我安装的是`"echarts": "^4.0.4"`，Echarts高版本已经不再内嵌全国地图和省份地图了，需要使用Echarts地图功能的同学就需要自己单独引入全国地图数据和省份地图数据了。

  - ![package](/img/vue-echarts/package.png)

- 写完之后，保存然后运行脚本`npm install`

### 1.2 在vue文件中引入`Echarts`
  ```js
    import echarts from 'echarts'
  ```

- 例如在`map.vue`中引入echarts后，我们就可以在`<script></script>`代码块中使用echarts了。

## 2 Echarts的使用基本过程
- 由于Vue框架的构造，我们在Vue项目中使用Echarts的过程和原生项目中使用Echart是类似的。不同的就是Vue的数据绑定方式对Echarts的更新渲染有一定的不同。要考虑到频繁操作dom来渲染echarts图表，浏览器是否会卡顿，cpu的使用率会攀升。这个在下面的章节介绍。

- 介绍一下基本的使用流程：
  
  ```html
  <template>
    <div class="mapBox">
        <div class="map1" ref="map1" id="map1"></div>
    </div>
    </template>

    <script>
    // 引入echarts
    import echarts from 'echarts' 
    import "echarts/map/js/china.js"
    import countImg from '@/assets/images/home/showCountImg.png'
    export default {
    name: 'mapBox',
    props: {

    },
    watch: {

    },
    data () {
        return {
        countImg
        }
    },
    methods: {
        // 初始化地图
        initMap () {
        // 这一步是要获取到元素
        this.map1 = echarts.init(this.$refs.map1)
        // 这一步是设置echarts的option配置项
        let options = {
            tooltip: {
            show: false
            },
            geo: {
            map: 'china', // 地图类型
            show: true, // 是否显示地理坐标系组件
            aspectScale: 0.75, //用于scale地图的长宽比
            zoom: 1.1, // 地图缩放比例
            roam: false, // 是否开启鼠标和平移漫游，默认不开启
            itemStyle: {
                normal: {
                areaColor: {
                    type: 'radial',
                    x: 700,
                    y: 520,
                    r: 300,
                    colorStops: [{
                    offset: 0,
                    color: '#0D72D3' // 0% 处的颜色
                    }, {
                    offset: 1,
                    color: '#08184C' // 100% 处的颜色
                    }],
                    global: true // 缺省为 false
                },
                borderWidth: 4, //设置外层边框
                borderColor: '#46DEFF',
                shadowColor: '#13527D',
                shadowOffsetX: 10,
                shadowOffsetY: 10
                },
                emphasis: {
                show: false,
                // areaColor: '#01215c'
                }
            }
            },
            // Series-related configures
            series: [ {
            type: 'map',
            map: 'china',
            roam: false,
            label: {
                normal: {
                show: true,
                textStyle: { // 未选中时，省份名字颜色
                    color: '#ffffff'
                }
                },
                emphasis: {
                textStyle: { // 选中时，省份名字颜色
                    color: '#ffffff'
                }
                }
            },
            itemStyle: {
                normal: {
                areaColor: {
                    color: {
                    type: 'radial',
                    x: 0.5,
                    y: 0.5,
                    r: 0.5,
                    colorStops: [{
                        offset: 0,
                        color: '#092462'
                    }, {
                        offset: 1,
                        color: '#1A8FDE'
                    }],
                    global: false
                }
                },
                borderColor: '#2581C1',
                borderWidth: 1,
                },
                emphasis: {
                areaColor: {
                    type: 'radial',
                    x: 0.5,
                    y: 0.5,
                    r: 0.8,
                    colorStops: [{
                    offset: 0,
                    color: '#5DAAFF'
                    },{
                    offset: 1,
                    color: '#0D75C0'
                    }]
                },
                borderColor: '#FFF467',
                borderWidth: 2
                }
            },
            zoom: 1.1
            }
            ]
        }
        this.$nextTick(() => {
            this.map1.setOption(options)
            on(window, "resize", this.resize);
        })
        }
    },
    mounted () {
        this.initMap()
    },
    created () {
    },
    beforeDestroy() {
        off(window, "resize", this.resize);
    }
    }
    </script>

    <style lang="less" scoped>
    .mapBox {
        width: 48%;
        height: 580px;
        background-size: 100% 100%;
        .map1 {
        margin-top: -30px;
        width: 110%;
        height: 580px;
        }
    }
  </style>
  ```

- 上面是一个简单的在Vue项目中使用Echarts map的例子，我删除了部分获取数据方法和其他一些在地图的操作的代码。(应该也能运行起来😓)

- 逐步解析：
  - 一. 在`<template></template>`模块中写入html标签
    - `<div class="map1" ref="map1" id="map1"></div>`，这是为了让Echarts知道将图表绘制在哪个地方。
    - 这里我使用了`ref`，这是Vue获取dom元素的一个命令`$refs`。

  - 二. 初始化一个Echarts实例
    - `this.map1 = echarts.init(this.$refs.map1)`
    - 同时这样的写法，也将这个Echarts实例绑到Vue实例中去了，这就是Vue的数据双向绑定。当然如果Echarts图表渲染往后，就不再变动，我们一般不会将实例绑到Vue实例中去。

  - 三. 设置实例的配置项
    - `let options = {}`
    - Echarts图表的一些配置，和操作都在这里面配置。当然也可以通过API来修改配置项

  - 四. 渲染图表
    - `this.map1.setOption(options)`
    - 到这里图表就渲染成功了。这里使用了`$nextTick`命令包裹。

## 3 Echarts地图服务
> 我在做需求时，最费时间的就是要在页面上显示一个全国的地图，并且还要和省份地图联动。
> - 功能点一是要在地图上打2种样式的label。到目前为止都是一些静态展示功能，除了国省地图联动。
> - 功能点二是地图上的点击操作，根据地图的不同，展示不同的东西
> - 功能点三是一个下拉选择框，根据选择的省份来显示不同的地图

### 3.1 引入地图数据
- 在第二章的例子里我已经引入了全国的地图
  `import "echarts/map/js/china.js"`

  - 作用是引入`node_modules`里的全国地图数据，（我使用的是低版本Echarts）.

  ![地图](/img/vue-echarts/map.png)

- 因为要做省份地图联动，所有就要引入所有省份的地图数据，要是像引入全国数据时的一个一个写，就太蠢了。

- 写了一个遍历文件夹的所有文件的方法，这样就引入了这个文件夹下的所有文件：

  ```js
    // 自动引入echarts/map/js/province/下的所有文件
    const modulesFiles = require.context('echarts/map/js/province', true, /.js$/)
    const modules = modulesFiles.keys().reduce((modules, modulePath) => {
    const moduleName = modulePath.replace(/^.\/(.*)\.js/,'$1')
    const value = modulesFiles(modulePath)
    modules[moduleName] = value.default
    return modules
    }, {})
  ```

  ![data](/img/vue-echarts/allMap.png)

- 到这里所需要的地图就全部引入了。这里使用`context()`方法，想进一步的同学也可以思考`import`和`require`的区别，面试会问到！

### 3.2 伪3D效果的地图
> 我需要绘制一个有3d效果的，国界明亮，省界呈暗色的一个地图

- 首先，使用配置里的`geo`项来配置一个全国的地图。

- 其次，设置`geo`项里面的`shadowOffsetX: 4, shadowOffsetY: 4`，就会呈现出伪3D的效果。这样要注意隐藏掉南海群岛，不然显示出来的样式就难看了。

- `geo`相关代码：

  ```js
        geo: {
          map: 'china', // 地图类型
          show: true, // 是否显示地理坐标系组件
          aspectScale: 0.75, //用于scale地图的长宽比
          zoom: 1.1, // 地图缩放比例
          roam: false, // 是否开启鼠标和平移漫游，默认不开启
          itemStyle: {
            normal: {
              areaColor: {
                type: 'radial',
                x: 700,
                y: 520,
                r: 300,
                colorStops: [{
                  offset: 0,
                  color: '#0D72D3' // 0% 处的颜色
                }, {
                  offset: 1,
                  color: '#08184C' // 100% 处的颜色
                }],
                global: true // 缺省为 false
              },
              borderWidth: 4, //设置外层边框
              borderColor: '#46DEFF',
              shadowColor: '#13527D',
              shadowOffsetX: 4,
              shadowOffsetY: 4
            },
            emphasis: {
              show: false,
              // areaColor: '#01215c'
            }
        },
          regions: [{
            name: '南海诸岛',
            itemStyle: {
              areaColor: 'rgba(0, 10, 52, 1)',
              borderColor: '#46DEFF',
              normal: {
                opacity: 0,
                label: {
                  show: false,
                  color: "#76EAFF",
                }
              }
            },
          }],
        },
  ```

- 至于省界和国界颜色不同，我们在`series`里面设置`borderWidth`，`borderColor`的值不同于`geo`里面的值就可以实现了

- `serise`部分代码：

  ```js
    series: [ {
          type: 'map',
          map: 'china', //使用
          roam: false,
          label: {
            normal: {
              show: true,
              textStyle: { // 未选中时，省份名字颜色
                color: '#ffffff'
              }
            },
            emphasis: {
              textStyle: { // 选中时，省份名字颜色
                color: '#ffffff'
              }
            }
          },
          itemStyle: {
            normal: {
              areaColor: {
                color: {
                  type: 'radial',
                  x: 0.5,
                  y: 0.5,
                  r: 0.5,
                  colorStops: [{
                    offset: 0,
                    color: '#092462'
                  }, {
                    offset: 1,
                    color: '#1A8FDE'
                  }],
                global: false
              }
            },
            borderColor: '#2581C1',
            borderWidth: 1,
            },
            emphasis: {
              areaColor: {
                type: 'radial',
                x: 0.5,
                y: 0.5,
                r: 0.8,
                colorStops: [{
                  offset: 0,
                  color: '#5DAAFF'
                },{
                  offset: 1,
                  color: '#0D75C0'
                }]
              },
              borderColor: '#FFF467',
              borderWidth: 2
            }
          },
          zoom: 1.1
        }
    ]
  ```
### 3.3 全国地图和省份地图切换
- 这是一个最基本的需求，毕竟我要看全国的项目情况，也要看具体某个省的项目情况。

- 我司的产品毕竟奇葩，设计2种：
  - 一是select下拉框切换地图
  - 二是点击省份上显示的label来切换地图

- 这里就用到了`Echarts`的`on()`

- 假设`this.map1`是Echarts的一个实例，如下：

  ```js
    let _this = let
    _this.map1.on('click', function (mapParam) {
        // console.log("点击地图获取到的参数：", mapParam)
        option.geo[0].map = mapName
        option.series[0].map = mapName
        _this.map1.clear()
        _this.map1.setOption(option)
    }

  ```

  - 做切换主要就是这几句起作用。其他代码就是要判断是哪种方式操作的。

- 下面贴出这地图内点击操作的代码：
 
  ```js
    this.$nextTick(() => {
        this.map1.setOption(options)

        let option = this.map1.getOption()
        let _this = this // 保存this指向vue实例
        this.map1.off('click') // 避免一次点击，多次调用
        this.map1.on('click', function (mapParam) {
          let mapName = mapParam.name
          let reg = '', itemvalue2 = ''
          _this.provinceList.map(item => {
            if (item.indexOf('省') !== -1) {
              reg = new RegExp("省","g")
              itemvalue2 = item.replace(reg,"")
            } 
            if (item.indexOf('自治区') !== -1) {
              if (item === '内蒙古自治区') {
                itemvalue2 = item.slice(0,3)
              } else {
                itemvalue2 = item.slice(0,2)
              }
            }
            if (item.indexOf('市') !== -1) {
              reg = new RegExp("市","g")
              itemvalue2 = item.replace(reg,"")
            }
            if (item.indexOf('特别行政区') !== -1) {
              reg = new RegExp("特别行政区","g")
              itemvalue2 = item.replace(reg,"")
            }
            if (mapName === itemvalue2) {
              _this.provinceName = item // 将点击进入的省赋值给当前省份下拉选择器
              option.geo[0].map = mapName
              option.series[0].map = mapName
              option.series[1].data = []
              _this.map1.clear()
              _this.map1.setOption(option)
            }
          })
          // 下面处理省级地图点击操作
          let mapParamData = mapParam.data
          if (mapParamData !== undefined && mapParamData.value.length === 4) {
            _this.isOpen = true
            _this.openInfo = mapParamData.value[3]
          }
        })
    })
  ```

  - 这份代码可以参考！

- 由于后端给我返回的省份下拉框的数据不符合Echarts地图数据的要求，我手动遍历改造了数据：
  
  ```js
        let reg = '', itemvalue2 = ''
        _this.provinceList.map(item => {
            if (item.indexOf('省') !== -1) {
              reg = new RegExp("省","g")
              itemvalue2 = item.replace(reg,"")
            } 
            if (item.indexOf('自治区') !== -1) {
              if (item === '内蒙古自治区') {
                itemvalue2 = item.slice(0,3)
              } else {
                itemvalue2 = item.slice(0,2)
              }
            }
            if (item.indexOf('市') !== -1) {
              reg = new RegExp("市","g")
              itemvalue2 = item.replace(reg,"")
            }
            if (item.indexOf('特别行政区') !== -1) {
              reg = new RegExp("特别行政区","g")
              itemvalue2 = item.replace(reg,"")
            }
        })
  ```

- 下拉框切换操作，就要考虑全国和省份2种地图了：
  
  ```js
    if (item === '全国') {
        itemvalue2 = 'china'
        _this.map1.clear()
         _this.initMap()
    }
  ```
  
  - 这里我就偷懒了，如果是全国，就直接初始化地图了😀

### 3.4 在地图上显示标签
- 这个其实利用的是`scatter`(气泡图)和里面的配置项`coordinateSystem: 'geo'`。

- 相关代码：
  
  ```js
        {
          name: '',
          type: 'scatter', // 气泡图
          coordinateSystem: 'geo', // 使用地理坐标系
          // symbol: 'pin',
          symbol: 'image://'+ countImg,
          symbolSize: [40,74],
          // symbolKeepAspect: true, // 缩放时保持比例
          symbolOffset: [0,'-50%'], // 偏移量
          label: {
            normal: {
              show: true,
              textStyle: {
                color: '#fff',
                fontSize: 14,
                padding: [-28, 0, 0, 0],
                fontWeight: 'normal'
              },
              formatter (value){
                return value.data.value[2]
              }
            }
          },
          itemStyle: {
            normal: {
              color: '#D8BC37', //标志颜色
            }
          },
          data: convertData(data), // 这个数据需要从后端获取
          showEffectOn: 'render',
          rippleEffect: {
            brushType: 'stroke'
          },
          hoverAnimation: true,
          zlevel: 1
        }
  ```

- 上面代码里的`converData()`方法：

  ```js
    let convertData = function (data) {
        let res = [];
        for (let i = 0; i < data.length; i++) {
          let geoCoord = geoCoordMap[data[i].name];
          if (geoCoord) {
            res.push({
              name: data[i].name,
              value: geoCoord.concat(data[i].value)
            });
          }
        }
        return res;
    }
  ```

  - `geoCoordMap`是一个全国各省省会城市的坐标数组集合，等下在文末粘出。

### 3.5 省份地图要展示的标签
> 由于全国地图和省份地图要展示的标签不同，所以`scatter`要在地图切换后随着改变样式和数据

- 省份标签样式：
   
    ```js
        let scatter = null
        scatter = {
            name: '',
            type: 'scatter', // 气泡图
            coordinateSystem: 'geo', // 使用地理坐标系
            symbol: 'image://'+ yellowIcon,
            symbolSize: [15,29],
            label: {
              normal: {
                show: false,
                textStyle: {
                  color: '#fff',
                  fontSize: 14,
                  padding: [-28, 0, 0, 0],
                  fontWeight: 'normal'
                },
                formatter (value){
                  return value.data.value[2]
                }
              }
            },
            itemStyle: {
              normal: {
                color: '#D8BC37',
              }
            },
            data: newMapData,
            showEffectOn: 'render',
            rippleEffect: {
              brushType: 'stroke'
            },
            hoverAnimation: true,
            zlevel: 1
          }
          option.series[1] = scatter
    ```

## 4 Echarts和Vue
> 在做项目的时候，页面要展示10个左右的Echarts图表（包括地图）。然后产品设计了一排按钮来更新页面中所有的图表。

> 针对这个需求，我先将每个图表封装成一个vue子组件，然后我就遇到了一些问题......

### 4.1 页面各个部分的组成
- 起初我将页面的组件分成3列，中间用来放置地图和一些操作。也就是说这个页面由3个子组件组成，设为`left.vue`，`map.vue`，`right.vue`

- 我需要在`map.vue`这个子组件中去操作其他子组件里面的图表更新

- 我本来可以将所有操作都放到父组件，然后在父组件获取全部数据，然后传给子组件去渲染。但是我已经把操作放到`map.vue`子组件了，又不想改。

- 所有我借助了`vuex`这个状态管理工具，来管理这个页面里所有的数据。

- 用`Event Bus`来调用子组件里面的方法去更新图表

### 4.2 遇到的问题
- 最致命的问题：
  - 操作几次之后页面变得非常卡

- 分析原因:
  - 更新Echarts图表时，没有销毁旧的图表实例，造成内存泄漏
  - 因为点击不同的按钮，都会先后端发送若干个请求，当请求结束后又会同时去渲染图表，造成卡顿
  - `Event Bus`没有用好，导致多次重复渲染图表（实际发现的问题，修复）
  - 由于每次都是初始图表，导致dom频繁销毁重建，页面卡顿

### 4.3 一些优化手段
- 每次更新前，清空图表而不是销毁，避免重新初始化
 
  ```js
  let _this = this
    let option = _this.echartObject.getOption()
    option.xAxis[0].data = _this.xAxisData
    option.series[0].data = _this.countData
     _this.echartObject.clear()
     _this.echartObject.setOption(option)
  ```

- 切换路由时销毁实例，避免造成内存泄漏
 
  ```js
    if (this.echartObject) {
        echarts.echartObject(this.area);
    }
  ```

- 在`beforeDestory`钩子函数里，销毁实例
  
  ```js
    beforeDestroy () {
        this.echartObject1.clear();
        this.echartObject2.clear();
    }
  ```

- 也要注意在使用辅助方法时造成重复渲染的问题

- 遇到问题多看Echarts API 和搜索问题，判断问题是代码写法问题、Vue数据绑定问题还是其他别的问题

## 5 总结
- 这算是最近2周写Echarts相关的一些总结。我虽然之前也断断续续的使用过Echarts来完成图表任务，但一直没有去真正的了解Echarts。
- 当然Javascript代码写法灵活，没有必要和我的写法一致。我也在一直突破自己简单的代码写作方式，希望自己的代码写的更优雅更高效。
- 由于项目工期非常短，我一般都不会去考虑优化的问题。我希望自己有时候还是要思考自己写的代码可以优化的地方，使用新的es标准重构的可能。
- Echarts的功能非常强大，许多找不到答案的疑惑就需要自己到API和配置中去解惑了！毕竟好多功能都没有用到，但Echarts设计者可能想到了。

## 下期预告
> Echarts功能已经非常强大了，但是在绘制3D图表时，体验就不好了。这时候我们就要使用`HightCharts`这个库了！