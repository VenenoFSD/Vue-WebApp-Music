<template>
    <div ref="wrapper">
        <slot></slot>
    </div>
</template>

<script>
    import BScroll from 'better-scroll'
    export default {
        name: "Scroll",
        props: {
            probeType: {
                type: Number,
                /* 0：即不派发 scroll 事件；1：非实时（屏幕滑动超过一定时间后）派发scroll 事件；2：屏幕滑动的过程中实时的派发 scroll 事件；3：不仅在屏幕滑动的过程中，而且在 momentum 滚动动画运行过程中实时派发 scroll 事件 */
                default: 1
            },
            click: {
                type: Boolean,
                default: true
            },
            data: {
                type: Array,
                default: null
            },
            listenScroll: {
                type: Boolean,
                default: false
            },
            pullUp: {  //  是否上拉加载
                type: Boolean,
                default: false
            },
            beforeScroll: {
                type: Boolean,
                default: false
            },
            refreshDelay: {
                type: Number,
                default: 20
            }
        },
        methods: {
            _initScroll () {
                if (!this.$refs.wrapper) {
                    return;
                }
                this.scroll = new BScroll(this.$refs.wrapper, {
                    probeType: this.probeType,
                    click: this.click
                });
                if (this.listenScroll) {
                    let This = this;
                    this.scroll.on('scroll', (pos) => {
                        This.$emit('scroll', pos);
                    });
                }
                if (this.pullUp) {
                    this.scroll.on('scrollEnd', () => {
                        if (this.scroll.y <= (this.scroll.maxScrollY + 50)) {
                            this.$emit('scrollToEnd');
                        }
                    });
                }
                if (this.beforeScroll) {
                    this.scroll.on('beforeScrollStart', () => {
                        this.$emit('beforeScroll');
                    });
                }
            },
            enable () {
                this.scroll && this.scroll.enable();
            },
            disable () {
                this.scroll && this.scroll.disable();
            },
            refresh () {
                this.scroll && this.scroll.refresh();
            },
            scrollTo () {
                this.scroll && this.scroll.scrollTo.apply(this.scroll, arguments);
            },
            scrollToElement () {
                this.scroll && this.scroll.scrollToElement.apply(this.scroll, arguments);
            }
        },
        mounted () {
            setTimeout(() => {
                this._initScroll();
            }, 20);
        },
        watch: {
            data () {
                setTimeout(() => {
                    this.refresh();
                }, this.refreshDelay);
            }
        }
    }
</script>

<style scoped>

</style>
