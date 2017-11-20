<template>
    <el-row class="warp">
        <el-col :span="24" class="warp-breadcrumb">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
                <el-breadcrumb-item>canvas学习</el-breadcrumb-item>
                <el-breadcrumb-item>基本绘制</el-breadcrumb-item>
            </el-breadcrumb>
        </el-col>
        <el-col :span="24" class="warp-main">
            <p class="first">
                画布配置 w=200,h100+,borderColor=#f0f0f0
            </p>
            <el-row :gutter="0" class="no1">
                <el-col :span="8">
                    <p>
                        <el-button type="default" @click="drawRect" size="small">画矩形</el-button>
                    </p>
                    <p>
                        <canvas id="myCanvas01" width="200" height="100"></canvas>
                    </p>
                </el-col>
                <el-col :span="16">
                    <p class="txtrow">在画布上绘制 150x75 的矩形，从左下角开始(0,0)。</p>
                    <p class="txtrow">let c = document.getElementById("myCanvas01");</p>
                    <p class="txtrow">let cxt = c.getContext("2d");</p>
                    <p class="txtrow">cxt.fillStyle = '#'+(Math.random()*0xffffff&lt;&lt;0).toString(16); #fillStyle方法将其染色</p>
                    <p class="txtrow">cxt.fillRect(0,0,150,75);</p>
                </el-col>
            </el-row>
            <el-row :gutter="0">
                <el-col :span="8">
                    <p>
                        <el-button @click="drawLine" size="small">画线</el-button>
                    </p>
                    <p>
                        <canvas id="myCanvas02" width="200" height="100"></canvas>
                    </p>
                </el-col>
                <el-col :span="16">
                    <p class="txtrow">通过指定从何处开始，在何处结束，来绘制一条线</p>
                    <p class="txtrow">let cont = document.getElementById("myCanvas02");</p>
                    <p class="txtrow">let cxt = cont.getContext("2d");</p>
                    <p class="txtrow">cont.height = cont.height;//重设画布高度，清空画布内容</p>
                    <p class="txtrow">cxt.moveTo(10,10);</p>
                    <p class="txtrow">cxt.lineTo(Math.random()*200,Math.random()*100);</p>
                    <p class="txtrow">cxt.lineTo(120,80);</p>
                    <p class="txtrow">cxt.stroke();</p>
                </el-col>
            </el-row>
            <el-row :gutter="0">
                <el-col :span="8">
                    <p>
                        <el-button @click="drawCircle" size="small">画圆</el-button>
                    </p>
                    <p>
                        <canvas id="myCanvas03" width="200" height="100"></canvas>
                    </p>
                </el-col>
                <el-col :span="16">
                    <p class="txtrow">通过规定尺寸、颜色和位置，来绘制一个圆</p>
                    <p class="txtrow">let cont = document.getElementById("myCanvas03");</p>
                    <p class="txtrow">let cxt = cont.getContext("2d");</p>
                    <p class="txtrow">cxt.fillStyle = '#' + (Math.random()*0xffffff&lt;&lg;0).toString(16);</p>
                    <p class="txtrow">cxt.beginPath();</p>
                    <p class="txtrow">cxt.arc(70,50,35,0,2*Math.PI,true);</p>
                    <p class="txtrow">cxt.closePath();</p>
                    <p class="txtrow">cxt.fill();</p>
                </el-col>
            </el-row>
            <el-row :gutter="0">
                <el-col :span="8">
                    <p>
                        <el-button @click="drawLinearGradient" size="small">画渐变背景</el-button>
                    </p>
                    <p>
                        <canvas id="myCanvas04" width="200" height="100"></canvas>
                    </p>
                </el-col>
                <el-col :span="16">
                    <p class="txtrow">绘制渐变背景</p>
                    <p class="txtrow">let cont = document.getElementById("myCanvas04");</p>
                    <p class="txtrow">let cxt = cont.getContext("2d");</p>
                    <p class="txtrow">let grd = cxt.createLinearGradient(0,0,200,100);</p>
                    <p class="txtrow">grd.addColorStop(0,this.randomColor());</p>
                    <p class="txtrow">grd.addColorStop(1,this.randomColor());</p>
                    <p class="txtrow">cxt.fillStyle = grd;</p>
                    <p class="txtrow">cxt.fillRect(0,0,200,100);</p>
                </el-col>
            </el-row>
            <el-row :gutter="0">
                <el-col :span="8">
                    <p>
                        <el-button @click="drawImage" size="small">画图片</el-button>
                    </p>
                    <p>
                        <canvas id="myCanvas05" width="200" height="160"></canvas>
                    </p>
                </el-col>
                <el-col :span="16">
                    <p class="txtrow">let cont = document.getElementById("myCanvas05");</p>
                    <p class="txtrow">cont.height = cont.height;//重设画布高度，清空画布内容</p>
                    <p class="txtrow">let cxt = cont.getContext("2d");</p>
                    <p class="txtrow">let img = new Image()</p>
                    <p class="txtrow">if(this.imageSeq === 1) {</p>
                    <p class="txtrow">this.imageSeq = 2;</p>
                    <p class="txtrow">img.src = ImageHill</p>
                    <p class="txtrow">}else{</p>
                    <p class="txtrow">this.imageSeq = 1;</p>
                    <p class="txtrow">img.src = ImageBook</p>
                    <p class="txtrow">}</p>
                    <p class="txtrow">img.onload = function(){</p>
                    <p class="txtrow">cxt.drawImage(img,0,0);</p>
                    <p class="txtrow">};</p>
                </el-col>
            </el-row>
            <el-row :gutter="0">
                <el-col :span="8">
                    <p>
                        <el-button @click="drawEllipse" size="small">画椭圆</el-button>
                    </p>
                    <p>
                        <canvas id="myCanvas06" width="200" height="160"></canvas>
                    </p>
                </el-col>
                <el-col :span="16">
                    <p class="txtrow">我在画椭圆，</p>
                    <p class="txtrow">你要画吗?</p>
                    <p class="txtrow">............</p>
                </el-col>
            </el-row>
            <el-row :gutter="0">
                <el-col :span="8">
                    <p>
                        <el-button @click="drawEllipseRect" size="small">画椭圆</el-button>
                    </p>
                    <p>
                        <canvas id="myCanvas07" width="200" height="200"></canvas>
                    </p>
                </el-col>
                <el-col :span="16">
                    <p class="txtrow">这个是？ 哦。是圆角矩形，</p>
                    <p class="txtrow">你要画吗?</p>
                    <p class="txtrow">............</p>
                </el-col>
            </el-row>
            <p class="footer"></p>
        </el-col>
    </el-row>
</template>

<script>

import ImageHill from '@/assets/images/hill.png'
import ImageBook from '@/assets/images/book.png'
export default {
    data() {
        return {
            imageSeq: 1
        }
    },
    methods: {
        drawRect() {
            //画矩形
            let cont = document.getElementById("myCanvas01");
            let cxt = cont.getContext("2d");
            cxt.fillStyle = this.randomColor();
            cxt.fillRect(0, 0, 150, 75);
        },
        //生成随机十六进制颜色
        randomColor() {
            return '#' + (Math.random() * 0xffffff << 0).toString(16);
        },
        drawLine() {
            //画线
            let cont = document.getElementById("myCanvas02");
            let cxt = cont.getContext("2d");
            cont.height = cont.height;//重设画布的高度，清空画布内容
            cxt.moveTo(10, 10);
            cxt.lineTo(Math.random() * 200, Math.random() * 100);
            cxt.lineTo(120, 80);
            cxt.stroke();
        },
        drawCircle() {
            //画圆
            let cont = document.getElementById("myCanvas03");
            let cxt = cont.getContext("2d");
            cxt.fillStyle = this.randomColor();
            cxt.beginPath();
            cxt.arc(100, 50, 35, 0, 2 * Math.PI, true);
            cxt.closePath();
            cxt.fill();
        },
        drawLinearGradient() {
            //画渐变背景
            let cont = document.getElementById("myCanvas04");
            let cxt = cont.getContext("2d");
            let grd = cxt.createLinearGradient(0, 0, 200, 100);
            grd.addColorStop(0, this.randomColor());
            grd.addColorStop(1, this.randomColor());
            cxt.fillStyle = grd;
            cxt.fillRect(0, 0, 200, 100);
        },
        drawImage() {
            //画图片
            let cont = document.getElementById("myCanvas05");
            let cxt = cont.getContext("2d");
            cxt.clearRect(0, 0, 200, 160);
            let img = new Image();
            console.log()
            if (this.imageSeq === 1) {
                this.imageSeq = 2;
                img.src = ImageHill;
            } else {
                this.imageSeq = 1;
                img.src = ImageBook;
            }
            img.onload = function() {
                cxt.drawImage(img, 0, 0);
            }
        },
        drawEllipse() {
            //画椭圆
            let cont = document.getElementById("myCanvas06");
            let cxt = cont.getContext("2d");
            cxt.clearRect(0, 0, 200, 160);
            for (let i = 40; i < 160; i = i + 20) {
                cxt.save();
                cxt.fillStyle = this.randomColor();
                cxt.beginPath();
                cxt.translate(i, 40);
                cxt.rotate(0);
                cxt.scale(2.5, 2);
                cxt.arc(0, 0, 10, 0, 2 * Math.PI, true);
                cxt.closePath();
                cxt.fill();
                cxt.restore();
            }
            for (let i = 50; i < 130; i = i + 20) {
                cxt.save();
                cxt.fillStyle = this.randomColor();
                cxt.beginPath();
                cxt.translate(150, i);
                cxt.rotate(90 * Math.PI / 180);
                cxt.scale(2.5, 2);
                cxt.arc(0, 0, 10, 0, Math.PI * 2, true);
                cxt.closePath();
                cxt.fill();
                cxt.restore();
            }
        },
        drawEllipseRect() {
            //画圆矩形
            let cont = document.getElementById("myCanvas07");
            let cxt = cont.getContext("2d");
            cxt.clearRect(0, 0, 200, 200);
            this.drawRoundRect(cxt, 40, 40, 100, 120, 10);
            cxt.strokeStyle = "#0078aa";
            cxt.stroke();

            let img = new Image();
            img.src = ImageBook;
            img.onload = function() {
                cxt.save();
                cxt.scale(0.6, 0.6);
                cxt.drawImage(img, 85, 100);
                cxt.restore();
            }
        },
        drawRoundRect(cxt, x, y, width, height, radius) {
            cxt.beginPath();
            cxt.arc(x + radius, y + radius, radius, Math.PI, Math.PI * 3 / 2);
            cxt.lineTo(width - radius + x, y);
            cxt.arc(width - radius + x, radius + y, radius, Math.PI * 3 / 2, Math.PI * 2);
            cxt.lineTo(width + x, height + y - radius);
            cxt.arc(width - radius + x, height - radius + y, radius, 0, Math.PI * 1 / 2);
            cxt.lineTo(radius + x, height + y);
            cxt.arc(radius + x, height - radius + y, radius, Math.PI * 1 / 2, Math.PI);
            cxt.closePath();
        }
    },
    mounted() {
        this.drawRect();
        this.drawLine();
        this.drawCircle();
        this.drawLinearGradient();
        this.drawImage();
        this.drawEllipse();
        this.drawEllipseRect();
    }
}
</script>


<style scoped>
.warp-breadcrumb {
    margin: 5px 10px 15px 0;
}

p.first {
    background-color: lightblue;
}

p.txtrow {
    margin: 5px 0;
    font-size: 13px;
}

canvas {
    border: 1px solid #f0f0f0;
}

.warp-main>.el-row:not(.no1) {
    border-top: 1px solid #f0f0f0;
}

p.footer {
    height: 50px;
}
</style>