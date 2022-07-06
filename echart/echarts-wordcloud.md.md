仪表盘：
``` dataviewjs
var echarts = app.plugins.plugins['obsidian-echarts'].echarts();
 
var option;

option = {
  series: [
    {
      type: 'gauge',
      startAngle: 180,
      endAngle: 0,
      min: 0,
      max: 1,
      splitNumber: 8,
      axisLine: {
        lineStyle: {
          width: 6,
          color: [
            [0.25, '#FF6E76'],
            [0.5, '#FDDD60'],
            [0.75, '#58D9F9'],
            [1, '#7CFFB2']
          ]
        }
      },
      pointer: {
        icon: 'path://M12.8,0.7l12,40.1H0.7L12.8,0.7z',
        length: '12%',
        width: 20,
        offsetCenter: [0, '-60%'],
        itemStyle: {
          color: 'auto'
        }
      },
      axisTick: {
        length: 12,
        lineStyle: {
          color: 'auto',
          width: 2
        }
      },
      splitLine: {
        length: 20,
        lineStyle: {
          color: 'auto',
          width: 5
        }
      },
      axisLabel: {
        color: '#464646',
        fontSize: 20,
        distance: -60,
        formatter: function (value) {
          if (value === 0.875) {
            return 'A';
          } else if (value === 0.625) {
            return 'B';
          } else if (value === 0.375) {
            return 'C';
          } else if (value === 0.125) {
            return 'D';
          }
          return '';
        }
      },
      title: {
        offsetCenter: [0, '-20%'],
        fontSize: 30
      },
      detail: {
        fontSize: 50,
        offsetCenter: [0, '0%'],
        valueAnimation: true,
        formatter: function (value) {
          return Math.round(value * 100) + '分';
        },
        color: 'auto'
      },
      data: [
        {
          value: 0.1,
          name: 'Grade Rating'
        }
      ]
    }
  ]
};
  
app.plugins.plugins['obsidian-echarts'].render(option, this.container)
```
---

``` dataviewjs
var echarts = app.plugins.plugins['obsidian-echarts'].echarts();
var option;

option = {
  tooltip: {
    formatter: '{a} <br/>{b} : {c}%'
  },
  series: [
    {
      name: 'Pressure',
      type: 'gauge',
      progress: {
        show: true
      },
      detail: {
        valueAnimation: true,
        formatter: '{value}'
      },
      data: [
        {
          value: 50,
          name: 'SCORE'
        }
      ]
    }
  ]
};
  
app.plugins.plugins['obsidian-echarts'].render(option, this.container)
```
---

``` dataviewjs
var echarts = app.plugins.plugins['obsidian-echarts'].echarts();
var option;

const gaugeData = [
  {
    value: 20,
    name: 'Perfect',
    title: {
      offsetCenter: ['0%', '-40%']
    },
    detail: {
      valueAnimation: true,
      offsetCenter: ['0%', '-30%']
    }
  },
  {
    value: 40,
    name: 'Good',
    title: {
      offsetCenter: ['0%', '-10%']
    },
    detail: {
      valueAnimation: true,
      offsetCenter: ['0%', '0%']
    }
  },
  {
    value: 60,
    name: 'Commonly',
    title: {
      offsetCenter: ['0%', '20%']
    },
    detail: {
      valueAnimation: true,
      offsetCenter: ['0%', '30%']
    }
  },
  {
    value: 50,
    name: '测试',
    title: {
      offsetCenter: ['0%', '50%']
    },
    detail: {
      valueAnimation: true,
      offsetCenter: ['0%', '60%']
    }
  }
];
option = {
  series: [
    {
      type: 'gauge',
      startAngle: 90,
      endAngle: -270,
      pointer: {
        show: false
      },
      progress: {
        show: true,
        overlap: false,
        roundCap: true,
        clip: false,
        itemStyle: {
          borderWidth: 1,
          borderColor: '#464646'
        }
      },
      axisLine: {
        lineStyle: {
          width: 55
        }
      },
      splitLine: {
        show: false,
        distance: 10,
        length: 20
      },
      axisTick: {
        show: false
      },
      axisLabel: {
        show: false,
        distance: 5
      },
      data: gaugeData,
      title: {
        fontSize: 14
      },
      detail: {
        width: 60,
        height: 14,
        fontSize: 14,
        color: 'auto',
        borderColor: 'auto',
        borderRadius: 20,
        borderWidth: 1,
        formatter: '{value}%'
      }
    }
  ]
}; 
app.plugins.plugins['obsidian-echarts'].render(option, this.container)
```


* echart 图片例子：https://echarts.apache.org/examples/en/index.html
* echart 技巧：
	* 最开始定义echarts:  var echarts = app.plugins.plugins['obsidian-echarts'].echarts();
	* 不需要官方例子中的：
		* var chartDom = document.getElementById('main');
		* var myChart = echarts.init(chartDom);
		* option && myChart.setOption(option);
	* 最后进行render: app.plugins.plugins['obsidian-echarts'].render(option, this.container)
 
