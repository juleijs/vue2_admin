<template>
    <el-row class="warp">
        <el-col :span="24" class="warp-breadcrumb">
            <el-breadcrumb separator="/">
                <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
                <el-breadcrumb-item>canvas学习</el-breadcrumb-item>
                <el-breadcrumb-item>贪吃蛇</el-breadcrumb-item>
            </el-breadcrumb>
        </el-col>
        <el-col class="warp-main">
            <p>space(空格键)控制开始、暂停。↑↓←→(上下左右方向键)控制蛇的移动方向。R键控制重玩。</p>
            <p>
                得分：
                <span class="score">{{snake.length - 3}}</span>
                <span class="states">{{statusText}}</span>
            </p>
            <p>
                <canvas id="canvas01" width="600px" height="400px">
                    您的浏览器不支持canvas，请更换其他浏览器试一试。
                </canvas>
            </p>
        </el-col>
    </el-row>
</template>

<script>

import _ from 'lodash'
export default {
    data() {
        return {
            snake: [],
            status: '',
            food: [],
            timer: null,
            direction: 'right'
        }
    },
    methods: {
        initPlat() {
            let _this = this;
            _this.snake = [[230, 170], [210, 170], [190, 170]];
            _this.food = [];
            _this.status = "";
            _this.timer = null;
            _this.direction = 'right';
            _this.createFood();
            _this.drawSnake();
        },
        run() {
            let _this = this;
            if (!_this.timer) {
                _this.timer = setInterval(function() {

                    let next = null;
                    //前进就是加一个头，并删除最后一点
                    let x0 = _this.snake[0][0];
                    let y0 = _this.snake[0][1];
                    //计算蛇头下个位置
                    if (_this.direction === "left") {
                        next = [x0 - 20, y0];
                    } else if (_this.direction === "up") {
                        next = [x0, y0 - 20];
                        _this.snake.unshift();
                    } else if (_this.direction === "down") {
                        next = [x0, y0 + 20];
                        _this.snake.unshift();
                    } else if (_this.direction === "right") {
                        next = [x0 + 20, y0]
                    }
                    if (next[0] <= 0 || next[0] >= 600 || next[1] <= 0 || next[1] >= 400) {
                        //如果下个位置超出游戏区域，game over
                        clearInterval(_this.timer);
                        _this.timer = null;
                        _this.status = "over"
                    } else if (_.some(_this.snake, function(item) {
                        return item[0] === next[0] && item[1] === next[1]
                    })) {
                        //装着自己了也game over
                        clearInterval(_this.timer);
                        _this.timer = null;
                        _this.status = "over"
                    } else {
                        _this.snake.unshift(next);

                        if (_this.food && _this.food.length && _this.snake[0][0] === _this.food[0] && _this.snake[0][1] === _this.food[1]) {
                            //吃了食物
                            _this.createFood();
                        } else {
                            _this.snake.splice(_this.snake.length - 1, 1);
                        }
                    }
                    _this.drawSnake()
                }, 200)
            }
        },
        createFood() {
            let _this = this;
            let arr = _.filter(_this.point, function(item) {
                return item.u === false;
            })
            let one = _.sample(arr);
            _this.food = [one.x, one.y];
        },
        drawSnake() {
            //清空区域
            this.cxt.clearRect(0, 0, 600, 400);

            //画背景
            this.cxt.save();
            let grd = this.cxt.createLinearGradient(0, 0, 600, 400);
            grd.addColorStop(0, "aliceblue");
            grd.addColorStop(1, "#cfe2f3");
            this.cxt.fillStyle = grd;
            this.cxt.fillRect(0, 0, 600, 400);
            this.cxt.restore();

            //画网格线
            this.cxt.save();
            this.cxt.beginPath();
            for (let i = 20; i < 600; i = i + 20) {
                this.cxt.moveTo(i, 0);
                this.cxt.lineTo(i, 0);
                this.cxt.strokeStyle = "#f0f0f0";
                this.cxt.stroke();
            }
            for (let j = 20; j < 400; j = j + 20) {
                this.cxt.moveTo(0, j);
                this.cxt.lineTo(600, j);
                this.cxt.strokeStyle = "#f0f0f0";
                this.cxt.stroke();
            }
            this.cxt.restore();

            //画食物
            this.cxt.save();
            this.cxt.beginPath();
            this.cxt.fillStyle = 'green';
            this.cxt.fillRect(this.food[0] - 10, this.food[1] - 10, 20, 20);
            this.cxt.closePath();
            this.cxt.fill();
            this.cxt.restore();

            //画蛇身
            this.cxt.save();
            this.cxt.fillStyle = 'deepskyblue';
            this.cxt.strokeStyle = '#00f';//边框颜色
            this.cxt.beginPath();
            for (let i = 1; i < this.snake.length; i++) {
                this.cxt.fillRect(this.snake[i][0] - 10, this.snake[i][1] - 10, 20, 20);
                this.cxt.closePath();
            }

            //画蛇头
            if (this.direction === "left") {
                this.cxt.arc(this.snake[0][0], this.snake[0][1], 10, 270, Math.PI, true);
                this.cxt.fillRect(this.snake[0][0], this.snake[0][1] - 10, 10, 20);
                this.cxt.arc(this.snake[this.snake.length - 1][0], this.snake[this.snake.length - 1][1], 10, 270, Math.PI, true);
            } else if (this.direction === "up") {
                this.cxt.arc(this.snake[0][0], this.snake[0][1], 10, 0, Math.PI, true);
                this.cxt.fillRect(this.snake[0][0] - 10, this.snake[0][1], 20, 10);
            } else if (this.direction === "right") {
                this.cxt.arc(this.snake[0][0], this.snake[0][1], 10, 90, Math.PI, true);
                this.cxt.fillRect(this.snake[0][0] - 10, this.snake[0][1] - 10, 10, 20);
            } else if (this.direction === "down") {
                this.cxt.arc(this.snake[0][0], this.snake[0][1], 10, 180, Math.PI, true);
                this.cxt.fillRect(this.snake[0][0] - 10, this.snake[0][1] - 10, 20, 10);
            }
            this.cxt.fill();
            this.cxt.restore();

            //画眼睛(白眼圈)
            this.cxt.save();
            this.cxt.beginPath();
            this.cxt.fillStyle = "#fff";
            if (this.direction === "left") {
                this.cxt.arc(this.snake[0][0] - 2, this.snake[0][1] - 4, 3, 0, Math.PI * 2, true);
                this.cxt.arc(this.snake[0][0] - 2, this.snake[0][1] + 4, 3, 0, Math.PI * 2, true);
            } else if (this.direction === "up") {
                this.cxt.arc(this.snake[0][0] - 4, this.snake[0][1] - 2, 3, 0, Math.PI * 2, true);
                this.cxt.arc(this.snake[0][0] + 4, this.snake[0][1] - 2, 3, 0, Math.PI * 2, true);
            } else if (this.direction === "right") {
                this.cxt.arc(this.snake[0][0] + 2, this.snake[0][1] - 4, 3, 0, Math.PI * 2, true);
                this.cxt.arc(this.snake[0][0] + 2, this.snake[0][1] + 4, 3, 0, Math.PI * 2, true);
            } else if (this.direction === "down") {
                this.cxt.arc(this.snake[0][0] - 4, this.snake[0][1] + 2, 3, 0, Math.PI * 2, true);
                this.cxt.arc(this.snake[0][0] + 4, this.snake[0][1] + 2, 3, 0, Math.PI * 2, true);
            }
            this.cxt.closePath();
            this.cxt.fill();
            this.cxt.restore();

            //画眼睛(黑眼珠)
            this.cxt.save();
            this.cxt.beginPath();
            this.cxt.fillStyle = "#000";
            if (this.direction === "left") {
                this.cxt.arc(this.snake[0][0] - 2, this.snake[0][1] - 4, 2, 0, Math.PI * 2, true);
                this.cxt.arc(this.snake[0][0] - 2, this.snake[0][1] + 4, 2, 0, Math.PI * 2, true);
            } else if (this.direction === "up") {
                this.cxt.arc(this.snake[0][0] - 4, this.snake[0][1] - 2, 2, 0, Math.PI * 2, true);
                this.cxt.arc(this.snake[0][0] + 4, this.snake[0][1] - 2, 2, 0, Math.PI * 2, true);
            } else if (this.direction === "right") {
                this.cxt.arc(this.snake[0][0] + 2, this.snake[0][1] - 4, 2, 0, Math.PI * 2, true);
                this.cxt.arc(this.snake[0][0] + 2, this.snake[0][1] + 4, 2, 0, Math.PI * 2, true);
            } else if (this.direction === "down") {
                this.cxt.arc(this.snake[0][0] - 4, this.snake[0][1] + 2, 2, 0, Math.PI * 2, true);
                this.cxt.arc(this.snake[0][0] + 4, this.snake[0][1] + 2, 2, 0, Math.PI * 2, true);
            }
            this.cxt.closePath();
            this.cxt.fill();
            this.cxt.restore();
        }
    },
    computed: {
        statusText() {
            switch (this.status) {
                case "running":
                    return "进行中...";
                case "pause":
                    return "暂停中";
                case "over":
                    return "游戏结束";
                default:
                    return "等待开始"
            }
        },
        point() {
            let plat = [];
            for (let i = 10; i < 600; i = i + 20) {
                for (let j = 10; j < 400; j = j + 20) {
                    if (_.some(this.snake, function(item) {
                        return item[0] === i && item[i] === j
                    })) {
                        plat.push({
                            x: i,
                            y: j,
                            u: true
                        })
                    } else {
                        plat.push({
                            x: i,
                            y: j,
                            u: false
                        })
                    }
                }
            }
            return plat;
        },
        cxt() {
            return document.getElementById("canvas01").getContext("2d");
        }
    },
    mounted() {
        let _this = this;
        _this.initPlat();
        document.onkeydown = function(event) {
            let e = event || window.event || arguments.callee.caller.arguments[0];
            if (e) {
                switch (e.keyCode) {
                    case 27:
                        //按 Esc
                        break;
                    case 82:
                        //按R键重玩
                        if (_this.timer) {
                            clearInterval(_this.timer);
                            _this.timer = null;
                        }
                        _this.initPlat();
                        break;
                    case 13:
                        //按Enter
                        break;
                    case 32:
                        //按space
                        if (_this.status === 'running') {
                            if (_this.timer) {
                                clearInterval(_this.timer)
                                _this.timer = null;
                            }
                            _this.status = "pause";
                        } else if (_this.status === 'pause' || _this.status === "") {
                            _this.status = "running";
                            _this.run();
                        }
                        break;
                    case 37:
                        //按左方向
                        if (!(_this.snake[0][0] - 20 === _this.snake[1][0] && _this.snake[0][1] === _this.snake[1][1])) {
                            _this.direction = "left";
                        }
                        break;
                    case 38:
                        // 按上方向
                        if (!(_this.snake[0][0] === _this.snake[1][0] && _this.snake[0][1] - 20 === _this.snake[1][1])) {
                            _this.direction = "up";
                        }
                        break;
                    case 39:
                        // 按右方向
                        if (!(_this.snake[0][0] + 20 === _this.snake[1][0] && _this.snake[0][1] === _this.snake[1][1])) {
                            _this.direction = "right";
                        }
                        break;
                    case 40:
                        // 按下方向
                        if (!(_this.snake[0][0] === _this.snake[1][0] && _this.snake[0][1] + 20 === _this.snake[1][1])) {
                            _this.direction = "down";
                        }
                        break;
                    default:
                        break;
                }
            }
        }
    }
}
</script>


<style scoped>
.warp-breadcrumb {
    margin: 5px 10px 15px 0;
}

.warp-main>p:nth-of-type(1) {
    margin-top: 0;
    margin-bottom: 5px;
    color: #555;
    font-size: 14px;
}

.warp-main>p:nth-of-type(2) {
    margin-top: 5px;
    margin-bottom: 0;
}

.warp-main>p:nth-of-type(3) {
    margin-top: 5px;
}

.score {
    color: red;
    padding-right: 15px;
}

.states {
    color: red;
}

canvas {
    border: 1px solid teal;
}
</style>