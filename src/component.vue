<template>
    <div ref="container" class="container" style="">
        <div class="content" ref="content">
            <slot>

            </slot>
        </div>
        <!--<div style="position: fixed;top:0px;left: 0px;background-color: rgba(0,0,0,0.5);color:#fff;padding: 5px">-->
        <!--left:{{values.left}}<br/>-->
        <!--top:{{values.top}}<br/>-->
        <!--zoom:{{values.zoom}}<br/>-->
        <!--</div>-->

    </div>
</template>
<style scoped>
    .container {
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        -o-user-select: none;
        user-select: none;
    }

    .content {
        -webkit-transform-origin: left top;
        -webkit-transform: translateZ(0);
        -moz-transform-origin: left top;
        -moz-transform: translateZ(0);
        -ms-transform-origin: left top;
        -ms-transform: translateZ(0);
        -o-transform-origin: left top;
        -o-transform: translateZ(0);
        transform-origin: left top;
        transform: translateZ(0);
    }

</style>

<script>
    import Raf from './scroller/Raf'
    import Animate from './scroller/Animate'
    import Scroller from './scroller/Scroller'

    let componnet = this;

    Raf(componnet);
    Animate(componnet);
    function fixCss(name, attr) {
        let cssObj = {};
        if (!attr || attr === '') {
            return cssObj;
        }
        cssObj[name] = attr;
        cssObj['-webkit-' + name] = attr;
        cssObj['-moz-' + name] = attr;
        cssObj['-ms-' + name] = attr;
        cssObj['-o-' + name] = attr;
        return cssObj;
    };
     function css(el, obj) {
        if (el && obj) {
            for (let i in obj) {
                if (el.style) {
                    el.style[i] = obj[i];
                }
            }
        }
    };


    // 注册
    export default {
        name: 'vue-smart-scroller',
        // 声明 props
        props: {
            touchEl:{
                default:null
            },
            contentWidth: {
                type: Number,
                default: 0
            },
            contentHeight: {
                type: Number,
                default: 0
            },
            options: {
                type: Object,
                default: function () {
                    return {}
                }
            }
        },
        data: function () {
            return {
                scroller: null,
                container: null,
                content: null,
                values: {left: 0, top: 0, zoom: 0},
                max: {left: 0, top: 0}
            }
        },
        methods: {
            render: function (left, top, zoom) {
                this.values = this.scroller.getValues();
                this.max.left = this.scroller.__maxScrollLeft
                this.max.top = this.scroller.__maxScrollTop
                this.$emit('render', this.values)
                //this.content.style['transform'] = 'translate(' + (-left) + 'px,' + (-top) + 'px) scale(' + zoom + ')';
                css(this.content,fixCss('transform','translate(' + (-left) + 'px,' + (-top) + 'px) scale(' + zoom + ')'))


               // this.content.style['-webkit-transform'] = 'translate(' + (-left) + 'px,' + (-top) + 'px) scale(' + zoom + ')';
            },
            updateSize: function () {
                let clientWidth = this.container.clientWidth;
                let clientHeight = this.container.clientHeight;
                let contentWidth = this.contentWidth;
                let contentHeight = this.contentHeight;
                if (this.contentWidth === 0) {
                    contentWidth = this.content.scrollWidth;
                }
                if (this.contentHeight === 0) {
                    contentHeight = this.content.scrollHeight;
                }

                //   console.log(clientWidth, clientHeight, contentWidth, contentHeight)

                this.scroller.setDimensions(clientWidth, clientHeight, contentWidth, contentHeight);
            }
        },

        mounted: function () {
            let self = this;
            let scroller = new Scroller(this.render, this.options, componnet);
            let container = this.$refs.container;
            this.content = this.$refs.content;
            this.scroller = scroller
            this.container = container;
            this.content.onmousedown = function (e) {
                e.preventDefault()
            };

            if(!this.touchEl){
                this.touchEl=container;
            }

            this.updateSize()

            if ('ontouchstart' in window) {
                container.addEventListener("touchstart", function (e) {
                    if (e.touches[0] && e.touches[0].target && e.touches[0].target.tagName.match(/input|textarea|select/i)) {
                        return;
                    }
                    self.updateSize()
                    scroller.doTouchStart(e.touches, e.timeStamp);
                    self.$emit('start')
                    e.preventDefault();
                }, false);
                document.addEventListener("touchmove", function (e) {
                    scroller.doTouchMove(e.touches, e.timeStamp, e.scale);
                    self.$emit('move')
                }, false);
                document.addEventListener("touchend", function (e) {
                    scroller.doTouchEnd(e.timeStamp);
                    self.$emit('end')
                }, false);
                document.addEventListener("touchcancel", function (e) {
                    scroller.doTouchEnd(e.timeStamp);
                    //self.$emit('cancel')
                }, false);

            } else {
                var mousedown = false;
                container.addEventListener("mousedown", function (e) {
                    if (e.target.tagName.match(/input|textarea|select/i)) {
                        return;
                    }

                    scroller.doTouchStart([{
                        pageX: e.pageX,
                        pageY: e.pageY
                    }], e.timeStamp);
                    mousedown = true;
                    self.$emit('start')
                }, false);
                document.addEventListener("mousemove", function (e) {
                    if (!mousedown) {
                        return;
                    }
                    scroller.doTouchMove([{
                        pageX: e.pageX,
                        pageY: e.pageY
                    }], e.timeStamp);

                    mousedown = true;
                    self.$emit('move')
                }, false);
                document.addEventListener("mouseup", function (e) {
                    if (!mousedown) {
                        return;
                    }
                    scroller.doTouchEnd(e.timeStamp);
                    mousedown = false;
                    self.$emit('end')
                }, false);
                container.addEventListener("mousewheel", function (e) {
                    scroller.doMouseZoom(e.wheelDelta, e.timeStamp, e.pageX, e.pageY);
                    // self.$emit('zoom')
                }, false);

            }

        }
    }
</script>