<template>
<main class="main">
    <transition name="slide">
        <sidebar class="sidebar"></sidebar>
    </transition>
    <transition name="slide-flex">
        <editor class="editor" v-model="editor.data.content"></editor>
    </transition>
    <transition name="slide-flex">
        <preview class="preview" :content="editor.data.content"></preview>
    </transition>
    <login v-if="!userInfo.data.isLogin"></login>
</main>
</template>

<script type="ts">
import { createComponent, reactive, watch } from '@vue/composition-api';

import { getData } from '../dataInjector';

import Sidebar from './Sidebar.vue';
// import Editor from '@toonote/md-editor';
import Preview from './Preview.vue';
import Login from './Login.vue';

export default createComponent({
    components: {
        Sidebar,
        Editor: () => import('@toonote/md-editor'),
        Preview,
        Login,
    },
    setup(props, ctx){
        const editor = reactive(getData('editor'));
        const userInfo = reactive(getData('userInfo'));

        let lastContent = editor.data.content;

        watch(() => {
            if(lastContent !== editor.data.content && typeof editor.data.content !== 'undefined'){
                ctx.root.$webClient.$emit('editor.change', {
                    content: editor.data.content
                });
                lastContent = editor.data.content;
            }
        });

        return {
            editor,
            userInfo,
        };
    }
});
</script>

<style>
*{
	padding:0;
	margin:0;
}
html,body,#wrapper{
	height: 100%;
}
body{
	font-family: "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", "WenQuanYi Micro Hei", sans-serif;
}

/*动画*/
.slide-enter-active, .slide-leave-active {
	transition: width .5s;
}
.slide-enter, .slide-leave-active {
	width: 0!important;
}
.slide-flex-enter-active, .slide-flex-leave-active {
	transition: flex .5s;
}
.slide-flex-enter, .slide-flex-leave-active {
	flex: 0!important;
}

.main{
	display: flex;
	height:100%;
	/* background:url('../images/bg.jpg') center 40% no-repeat; */
}
.main.withSidebar{
	/* background-position: 60% 40%; */
}
/* .main.withMenubar{
	height:calc(100% - 24px);
} */
.main .sidebar{
    width: 250px;
}
.main .editor,
.main .preview{
    flex: 1;
}


/* 全局图标定义 */
.icon::before{
	content:' ';
	display: inline-block;
	width:16px;
	height:16px;
	vertical-align: sub;
	background-size:16px 16px;
	background-repeat:no-repeat;
}
</style>
