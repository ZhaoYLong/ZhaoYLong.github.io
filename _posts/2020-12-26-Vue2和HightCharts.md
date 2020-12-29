---
layout:     post
title:      Vue2和HighCharts的奇妙反应

subtitle:   使用hightcharts绘制3D饼图
date:       2020-12-26
header-img: img/post-bg-debug.png
catalog:    false
tag:
    - Vue
    - HighCharts
    - Front End
---

> 做大数据屏幕展示数据，3D图算是一种毕竟有冲击力的展示方式了。所有这次说一说在Vue2项目里如何使用HighCharts

- 这次主要绘制2种3D饼图
  - 高度一致的3D饼图
  - 高度不一致的3D饼图

## 1 Vue引入HighCharts

```shell
    npm install highcharts
    npm install vue-highcharts
```

### 1.1 在`main.js`中引入’

```js
    import highcharts from 'highcharts'
    import VueHighCharts from 'vue-highcharts'
    import highcharts3d from 'highcharts/highcharts-3d'

    // 引入3d组件
    highcharts3d(highcharts)
```

### 1.2 在Vue中的基本使用
- 相关代码：
  
  ```html
    <div class="pie" ref="domPie" id="domPie"></div>
  ```
  
  ```js
    import Highcharts from 'highcharts'

    let optionPie = {
        chart: {
          type: 'pie', // 饼图
          backgroundColor: 'transparent',
          height: 200,
          animation: false,
          options3d: {
            enabled: true,
            alpha: 60,
            beta: 0
          }
        },
        colors: [
          '#336EFF',
          '#5DD28A',
          '#69FCFF',
          '#6536FC'
        ],
        title: {
          text: '',
        },
        subtitle: {
          text: ''
        },
        credits: {
          enabled: false
        },
        plotOptions: {
          pie: {
            allowPointSelect: true,//每个扇块能否选中
            cursor: 'pointer',//鼠标指针
            depth: 20,//饼图的厚度
            dataLabels: {
              enabled: false,//是否显示饼图的线形tip
            }
          }
        },
        series: [
          {
            type: 'pie',
            name: '',
            data: data
          }
        ]
    }
      let that = this
      that.$nextTick(() => {
        Highcharts.chart(that.$refs.domPie, optionPie)
        on(window, "resize", this.resize);
    })
  ```

## 2 高度一致的3D饼图

- 高度一致的饼图是最基本的用法
  - 将配置里的`plotOptions`里的`pie`里的`depth`设置成一个数值就可以了。

  ```js
    plotOptions: {
        pie: {
            depth: 20,//饼图的厚度
        }
    }
  ```


## 3 高度不一致的3D饼图
- 高度不一致的3D饼图处理有些困难，但是还是要克服！

- 相关代码： 

```html
<template>
  <div class="project">
        <div class="body-pie">
          <!-- 3d图 -->
          <div ref="domAll" id="domAll" style="height: 200px;"></div>
        </div>
  </div>
</template>

<script>
import Highcharts from 'highcharts'
var each = Highcharts.each,
	round = Math.round,
	cos = Math.cos,
	sin = Math.sin,
	deg2rad = Math.deg2rad;
export default {
  name: 'project',
  props: {

  },
  watch: {

  },
  computed: {

  },
  data () {
    return {
      projectAllData: [],
    }
  },
  methods: {
    // 绘制高度不同的3d环形图
    initHightDepth3D () {
      // 保持vue对象，这个this指向vue实例
      let that = this
      // console.log("that: this:", that)
			Highcharts.wrap(Highcharts.seriesTypes.pie.prototype, 'translate', function(proceed) {
        proceed.apply(this, [].slice.call(arguments, 1));
        // 这个this指向hightcharts对象实例
				var series = this,
					chart = series.chart,
					options = chart.options,
					seriesOptions = series.options,
					depth = seriesOptions.depth || 0,
					options3d = options.chart.options3d,
					alpha = options3d.alpha,
					beta = options3d.beta,
          z = seriesOptions.stacking ? (seriesOptions.stack || 0) * depth : series._i * depth;
        // console.log("series:this: ", series)
				z += depth / 2;
				if (seriesOptions.grouping !== false) {
					z = 0;
				}
				each(series.data, function(point) {
					var shapeArgs = point.shapeArgs,
						angle;
					point.shapeType = 'arc3d';
					var ran = point.options.h;
					shapeArgs.z = z;
					shapeArgs.depth = depth * 0.75 + ran;
					shapeArgs.alpha = alpha;
					shapeArgs.beta = beta;
					shapeArgs.center = series.center;
					shapeArgs.ran = ran;
					angle = (shapeArgs.end + shapeArgs.start) / 2;
					point.slicedTranslation = {
						translateX: round(cos(angle) * seriesOptions.slicedOffset * cos(alpha * deg2rad)),
						translateY: round(sin(angle) * seriesOptions.slicedOffset * cos(alpha * deg2rad))
					};
				});
			});
			(function(H) {
				H.wrap(Highcharts.SVGRenderer.prototype, 'arc3dPath', function(proceed) {
					// Run original proceed method
					var ret = proceed.apply(this, [].slice.call(arguments, 1));
					ret.zTop = (ret.zOut + 0.5) / 100;
					return ret;
				});
			}(Highcharts));
      let options = {
        chart: {
					type: 'pie',
          animation: false,
          height: 200,
					backgroundColor: 'transparent',
					events: {
						load: function() {
							var each = Highcharts.each,
								points = this.series[0].points;
							each(points, function(p, i) {
								p.graphic.attr({
									translateY: -p.shapeArgs.ran
								});
								p.graphic.side1.attr({
									translateY: -p.shapeArgs.ran
								});
								p.graphic.side2.attr({
									translateY: -p.shapeArgs.ran
								});
							});
						}
					},
					options3d: {
						enabled: true,
						alpha: 60,
						beta: 0
          },
        },
        colors: [
          '#5DD28A',
          '#FFD43B',
          '#FF5E73',
        ],
				title: {
					text: ''
				},
				subtitle: {
					text: ''
				},
				credits: {
          enabled: false
        },
				plotOptions: {
					pie: {
						allowPointSelect: true,
						cursor: 'pointer',
						depth: 35,
						innerSize: 80,
						dataLabels: {
							enabled: false
						}
					}
				},
				series: [{
					type: 'pie',
					name: '',
					data: that.projectAllData
				}]
      }
			that.$nextTick(() => {
        Highcharts.chart(that.$refs.domAll, options)
        on(window, "resize", this.resize);
      })
    }
  },
  mounted () {
    this.initHightDepth3D()
  },
  created () {

  },
  beforeDestroy() {

  }
}
</script>

<style lang="less" scoped>

</style>

```

## 4 一些问题
- 当一个项目中高度不同的3D饼图和高度相同的饼图同时存在，要改变饼图的数据格式为：

  ```js
    let data3D  = []
    res.data.data.map(item => {
        data3D.push({
            name: item.name,
            y: item.data,
            h: 20 // 厚度（高度）每条数据的h一致，就是相同高度，不一致就是不同高度
        })
    })
  ```

## 5 总结
- 对highcharts仅仅是使用，对其API并不是很熟悉！