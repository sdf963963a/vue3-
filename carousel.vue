<template>
    <div class="banner-box" ref="root">
        <ul class="wrapper">
            <li v-for="(item, index) in dataSource" :key="item.id" :class="item.className" :style="item.sty" @click="slidActive(index)">          
                <div class="mark" :style="item.sty2"></div>
                <div class="iconHover flexBasic flexBetween">
                    <span>09/10 周五</span>
                    <span class="arrow-span"><i class="el-icon-plus"></i></span>
                </div>
            </li>
        </ul>
        <div class="arrow">
            <span class="arrow-span" @click="change('left')"><i class="el-icon-arrow-left"></i></span> 
            <span class="arrow-span" @click="change('right')"><i class="el-icon-arrow-right"></i></span>
        </div>
    </div>
</template>

<script>
import {
  onMounted,
  reactive, 
  toRefs,
  watch,
  ref
} from "vue";
import yyTest from '@/assets/images/test/yy-test.png'

export default {
    props: {
        initial: {
            type: Number,
            default: 0
        },
        interval: {
            type: Number,
            default: 2000
        }
    },
    setup(props) {
        console.log(props)
        const diaData= reactive({
            activeIndex: 2,
            initial: props.initial,
            dataSource: [
                {name: '法外狂徒张三',url: yyTest, id: 1},
                {name: '法外狂徒李四',url: yyTest, id: 2},
                {name: '法外狂徒王五',url: yyTest, id: 3},
                {name: '法外狂徒马六',url: yyTest, id: 4},
                {name: '法外狂徒马七',url: yyTest, id: 5},
            ]
        })
        // 2.处理每一项的样式
        const computed = (initial, source)=> {
            // 确保五项索引的合法性
            let len = source.length;
            initial = initial < 0 ? 0 : initial >= len ? len - 1 : initial;
            let temp1 = initial-2,
                temp2 = initial-1,
                temp3 = initial,
                temp4 = initial+1,
                temp5 = initial+2;
                temp1 < 0 ? (temp1 = len + temp1) : null;
                temp2 < 0 ? (temp2 = len + temp2) : null;
                temp4 >= len?temp4=temp4-len : null;
                temp5 >= len?temp5=temp5-len : null;
                temp3 = temp3 < 0 ? 0 : temp3 >= len ? len-1: temp3; 

                // 计算每一项的样式
                return source.map((item, index) => {
                    let transform = `translate(-50%,-50%) scale(0.55)`,
                        zIndex = 0,
                        className = 'slide',
                        background=`transparent`,
                        zIndeMark = 0,
                        opacity = `1`;
                    switch(index) {
                        case temp3:
                            zIndex = 4;
                            zIndeMark = 0;
                            className = 'slide active';
                            transform = `translate(-50%,-50%) scale(1)`
                            opacity = `1`
                            background= `transparent`
                            break;
                        case temp1:
                            zIndex = 2;
                            zIndeMark = 2;
                            className = 'slide';
                            transform = `translate(-180%,-50%) scale(0.9)`
                            opacity = `0.8`
                            background= `#fff`
                            break;
                        case temp2:
                            zIndex = 3;
                            zIndeMark = 3;
                            className = 'slide';
                            transform = `translate(-130%,-50%) scale(0.95)`
                            opacity = `0.8`
                            background= `#fff`
                            break;
                        case temp4:
                            zIndex = 3;
                            zIndeMark = 3;
                            className = 'slide';
                            transform = `translate(30%,-50%) scale(0.95)`
                            opacity = `0.8`
                            background= `#fff`
                            break;
                        case temp5:
                            zIndex = 2;
                            zIndeMark = 2;
                            className = 'slide';
                            transform = `translate(80%,-50%) scale(0.9)`
                            opacity = `0.8`
                            background= `#fff`
                            break; 
                    }
                    item.sty = {
                        transform,
                        zIndex,
                    };
                    item.sty2 = {
                        zIndex: zIndeMark,
                        opacity,
                        background
                    };
                    console.log(opacity)
                    item.className = className;
                    return item;
                })
        }
        // 监听inital,重新计算每一项的样式
        watch(
            () => diaData.initial,
            (newInitial, oldInitial) => {
                console.log('这是newIndex', newInitial)
                console.log('这是oldInitial', oldInitial)
                diaData.dataSource = computed(newInitial, diaData.dataSource)
            }
        )
        // 4.自动轮播
        let autoTimer = null;
        // const autoPlay =()=> {
        //     autoTimer = setInterval(() => {
        //         diaData.initial++;
        //         if(diaData.initial >= diaData.dataSource.length) {
        //             diaData.initial = 0;
        //         }
        //     }, props.interval)
        // }

        // 5.页面第一次渲染完：开启自动轮播--鼠标进入离开控制自动轮播
        let root = ref(null);
        onMounted(() => {
            diaData.dataSource = computed(props.initial, diaData.dataSource)
            // autoPlay();
            // let box = root.value;
            // box.onmouseenter = () => clearInterval(autoTimer);
            // box.onmouseleave = () => autoPlay(); 
        })

        // 6.点击左右切换
        const change =(dir) => {
            if(dir == 'right') {
                diaData.initial++;
                diaData.initial >= diaData.dataSource.length ? (diaData.initial = 0) : null;
                return
            }
                diaData.initial--;
                diaData.initial < 0 ? (diaData.initial = diaData.dataSource.length-1) : null;
        }

        // 点击切换
        const slidActive =(index)=> {
            diaData.dataSource = computed(index, diaData.dataSource)
        }

        return {
            ...toRefs(diaData),
            root,
            yyTest,
            autoTimer,
            change,
            slidActive
        } 
    },
}
</script>

<style lang="scss" scoped>
    .banner-box {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        width: 100%;
        height: 85%;
        .wrapper {
            position: relative;
            height: 93%;
            box-sizing: border-box;
            .slide {
                position: absolute;
                top: 50%;
                left: 50%;
                transform: translate(-50%, -50%);
                height: 100%;
                width: 25%;
                box-shadow: 0 4px 16px 0 rgb(38 130 216 / 10%);
                overflow: hidden;
                transition: 0.5s;
                border-radius: 16px;
                background: #fff;
                padding: 20px;
                img {
                    width: 100%;
                    height: 95%;
                    display: block;
                }
                .mark {
                    position: absolute;
                    top: 0;
                    left: 0;
                    width: 100%;
                    height: 100%;
                    opacity: 0.8;
                    transition: 0.3s;
                    // background: #fff;
                    // z-index: 1;
                }
                &.active .mark, &:hover .mark{
                    // background: rgba(0, 0, 0, 0);
                    // z-index: 0;
                    // opacity: 0.8;
                }
            }
            span {
                cursor: pointer;
                 z-index: 1;
            }
        }
        .arrow {
            text-align: center;
            .arrow-span {
                display: inline-block;
                height: 30px;
                width: 30px;
                border-radius: 30px;
                line-height: 30px;
                cursor: pointer;
                margin: 20px;
                &:hover {
                    background: #ececec;
                }
                .el-icon-arrow-left {
                    font-size: 16px;
                    font-weight: bold;
                }
                .el-icon-arrow-right {
                    font-size: 16px;
                    font-weight: bold;
                }
            }
        }
        .iconHover {
            .arrow-span {
                display: inline-block;
                height: 30px;
                width: 30px;
                border-radius: 30px;
                line-height: 30px;
                cursor: pointer;
                text-align: center;
                &:hover {
                    background: #ececec;
                }
            }
        }
    }
    

</style>
