<template>
    <div style="width:100%;height:100%;z-index:10" ref="chart" id="common_horizontalBarGraph"></div>
</template>

<style scoped>
</style>

<script>
    import echarts from "echarts";

    export default {
        data() {
            return {
                timerId: 0
            };
        },
        mounted() {
            this.mycharts();
        },
        watch: {
            Data: {
                handler(newval, old) {
                    if (newval.countryData.length || newval.worldData.length) {
                        this.mycharts();
                    }
                },
                deep: true
            }
        },
        props: {
            Data: {
                type: Object,
                default: () => {
                    return {};
                }
            }
        },
        methods: {
            handleResize() {
                var bar_dv = this.$refs.chart;
                echarts.init(bar_dv).resize();
            },
            mycharts() {
                var _this = this;
                var bar_dv = this.$refs.chart;
                var charts = echarts.init(bar_dv, "shine");

                let option = {

                    // title: {
                    //   text: this.Data.titleText,
                    //   textStyle: {
                    //     color: '#ccc',
                    //   }
                    // },
                    tooltip: {
                        trigger: "axis",
                        axisPointer: {
                            // 坐标轴指示器，坐标轴触发有效
                            type: "shadow" // 默认为直线，可选为：'line' | 'shadow'
                        }
                    },
                    legend: {
                        data: this.Data.legendData,
                        textStyle: {
                            color: "#ccc"
                        },
                        top:40
                    },
                    textStyle: {
                        color: "#ccc"
                    },
                    toolbox: {
                        top:'top',
                        right: 10,
                        show: true,
                        feature: {
                            saveAsImage: {
                                title:"Save as a picture",
                                pixelRatio:2,
                                backgroundColor:'transparent'
                            },
                            dataView: {
                                lang: ['DataView','Close'],
                                title:"DataView",
                                readOnly: true,
                                textColor: '#fff',
                                backgroundColor: '#09132B',
                                buttonColor: '#fff',
                                buttonTextColor: '#000',
                                iconStyle:{
                                    color:'#409EFF',
                                    borderColor:'#245632'
                                },

                                optionToContent: function (opt) {
                                    console.log(opt);
                                    let series = opt.series;
                                    let datas = series.filter(element => {
                                        return element.name != undefined;
                                    })
                                    if (datas.length == 1) {
                                        let tdHeads = '<td  style="padding: 0 10px ;font-Size:30px">category</td><td  style="padding: 0 10px;font-Size:40px">world</td>'; //表头
                                        let tdBodys = ''; //数据
                                        let data = datas[0].data

                                        let table = `<table width='100%' border="1" style="border-collapse:collapse;text-align:center;color:#ccc; border:solid"><tbody><tr>${tdHeads} </tr>`;
                                        data.forEach((ele, index) => {
                                            tdBodys = `<td style="border:solid 1px">${opt.yAxis[0].data[index]}</td><td style="border:solid 1px">${ele}</td>`
                                            table += `<tr>${tdBodys}</tr>`
                                        });
                                        table += '</tbody></table>';

                                        return table;
                                    }
                                    if (datas.length == 2) {

                                        let tdHeads = `<td  style="padding: 0 10px ;font-Size:30px">country</td><td  style="padding: 0 10px;font-Size:30px">${opt.legend[0].data[1]}</td><td  style="padding: 0 10px;font-Size:30px">${opt.legend[0].data[0]}</td>`; //表头
                                        let tdBodys = ''; //数据
                                        let data = datas[0].data
                                        let table = `<table width='100%' border="1" style="border-collapse:collapse;text-align:center;color:#ccc; border:solid"><tbody><tr>${tdHeads} </tr>`;
                                        data.forEach((element, index) => {
                                            tdBodys = `<td style="border:solid 1px">${opt.yAxis[0].data[index]}</td><td style="border:solid 1px">${element}</td><td style="border:solid 1px">${datas[1].data[index]}</td>`
                                            table += `<tr>${tdBodys}</tr>`
                                        });
                                        table += '</tbody></table>';

                                        return table;
                                    }
                                }
                            },
                        }
                    },
                    // dataZoom: [
                    //   {
                    //     type: 'slider',
                    //     show: true,
                    //     start: 1,
                    //     end: 100,
                    //     handleSize: 8
                    //   },
                    //   {
                    //     type: 'inside',
                    //     start: 1,
                    //     end: 100
                    //   },
                    // {
                    //     type: 'slider',
                    //     show: true,
                    //     yAxisIndex: 0,
                    //     filterMode: 'empty',
                    //     width: 12,
                    //     height: '70%',
                    //     handleSize: 8,
                    //     showDataShadow: false,
                    //     left: '93%'
                    // }
                    //],
                    grid: {
                        left: "5%",
                        right: "4%",
                        bottom: "5%",
                        top:'10%',
                        containLabel: true
                    },
                    xAxis: {
                        type: "value",
                        axisLine: {
                            show: false
                        },
                        splitLine: {
                            show: false
                        },
                        axisLabel: {
                            formatter: function (value, index) {
                                if (value >= 100000000) {
                                    return value / 100000000 + 'B'
                                }
                                if (value >= 1000000) {
                                    return value / 1000000 + 'M'
                                }
                                return value
                            }
                        }

                    },
                    yAxis: {
                        type: "category",
                        data: this.Data.yTitle,
                        splitLine: {
                            show: false
                        },
                        // axisLabel :{

                        //   },

                        axisLabel: {
                            interval: 0,
                            inside:true,
                            //rotate:-45,
                            //fontSize:6,
                            // formatter: function (value, index) {
                            //   let res = "";
                            //   value.split(' ').forEach(ele => {
                            //     if(ele.length>10)
                            //     {
                            //     res += `${ele}
                            //   `}else
                            //   {
                            //     res += ele
                            //   }
                            //   })
                            //   return res.trim();
                            // }
                        },
                        z:10
                    },
                    series: [
                        {
                            name: this.Data.legendData[1],
                            type: "bar",

                            barWidth: 10,

                            label: {
                                normal: {
                                    show: false,
                                    // position: "insideRight"
                                    position: "right"
                                }
                            },
                            itemStyle: {
                                //图形样式
                                normal: {
                                    barBorderRadius: [5, 5, 5, 5],
                                    color: new echarts.graphic.LinearGradient(
                                        0,
                                        0,
                                        0,
                                        1,
                                        [
                                            {
                                                offset: 1,
                                                color: "rgba(127, 128, 225, 0.7)"
                                            },
                                            {
                                                offset: 0.9,
                                                color: "rgba(72, 73, 181, 0.7)"
                                            },
                                            {
                                                offset: 0.31,
                                                color: "rgba(0, 208, 208, 0.7)"
                                            },
                                            {
                                                offset: 0.15,
                                                color: "rgba(0, 208, 208, 0.7)"
                                            },
                                            {
                                                offset: 0,
                                                color: "rgba(104, 253, 255, 0.7)"
                                            }
                                        ],
                                        false
                                    )
                                }
                            },

                            data: this.Data.legendData.length == 1 ? [] : this.Data.countryData
                        },
                        {
                            name: "world",
                            type: "bar",

                            barWidth: 10,
                            itemStyle: {
                                //图形样式
                                normal: {
                                    barBorderRadius: [5, 5, 5, 5],
                                    color: new echarts.graphic.LinearGradient(
                                        0,
                                        0,
                                        0,
                                        1,
                                        [
                                            {
                                                offset: 1,
                                                color: "rgba(127, 128, 225, 0.7)"
                                            },
                                            {
                                                offset: 0.9,
                                                color: "rgba(72, 73, 181, 0.7)"
                                            },
                                            {
                                                offset: 0.25,
                                                color: "rgba(226, 99, 74, 0.7)"
                                            },
                                            {
                                                offset: 0,
                                                color: "rgba(253, 200, 106, 0.7)"
                                            }
                                        ],
                                        false
                                    )
                                }
                            },
                            label: {
                                normal: {
                                    show: false,
                                    position: "right"
                                }
                            },
                            data: this.Data.legendData.length == 1 ? this.Data.countryData : this.Data.worldData
                        }
                    ]
                };

                //清空画布
                //charts.clear();
                charts.setOption(option);
                // 屏幕自适应
                this.timerId = setTimeout(function () {
                    window.addEventListener("resize", _this.handleResize);
                }, 200);
            }
        },
        beforeDestroy() {
            clearTimeout(this.timerId);
            window.removeEventListener("resize", this.handleResize);
        }

    };
</script>

