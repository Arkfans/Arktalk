<script setup>
    import {ref, inject, computed, watch} from 'vue';
    import {uuid} from '@/lib/tool'

    const chars = inject('chars');
    const images = inject('images');
    const staticUrl = inject('staticUrl');
    const renderSettings = inject('renderSettings');
    const width = inject('width');
    const charDirection = inject('charDirection');
    const preScreenshot = inject('preScreenshot');
    const props = defineProps(['data', 'index']);
    defineEmits(['edit']);

    const data = computed(() => props.data);
    const index = computed(() => props.index);

    const char = computed(() => {
        return chars.value[data.value.char] || {};
    });
    const id = ref(uuid());
    const right = computed(() => {
        if (data.value.char) {
            // opposite is deprecated
            // return data.opposite ? !char.value.right : !!char.value.right
            return !!char.value.right
        } else {
            return null
        }
    });

    function resizeImage() {
        if (data.value.type === 'image') {
            id.value = uuid();
        }
    }

    watch(() => width.value.window, () => {
        // window改变时调整图片大小
        resizeImage()
    });
    watch(charDirection, () => {
        // 头像列改变时调整图片大小
        resizeImage()
    });
</script>

<template>
    <div :class="renderSettings.style">
        <div class="dialogue">
            <div style="display: flex; width: 100%; margin-bottom: 10px;" @click="$emit('edit', index)"
                 :style="{justifyContent:(right?'flex-end':'flex-start')}">
                <template v-if="data.type==='title'">
                    <div class="title-box">
                        <div style="background: darkgrey; padding: 0 7px; font-size: 10px">DIALOGUE</div>
                        <div style="color: var(--dialogue-color); padding: 0 10px; position:relative;">
                            <div class="block" style="top: 0; left: 0"></div>
                            <div class="block" style="bottom: 0; left: 0"></div>
                            <div class="block" style="top: 0; right: 0"></div>
                            <div class="block" style="bottom: 0; right: 0"></div>
                            <pre>{{data.content}}</pre>
                        </div>
                        <div class="line"></div>
                    </div>
                </template>
                <template v-else>
                    <div v-if="charDirection[0]" class="avatar" style="margin-right: 10px">
                        <div v-if="right === false">
                            <img src="/avatar-bg.png">
                            <img :src="images[char.avatar] || staticUrl + char.avatar">
                        </div>
                    </div>
                    <div v-if="data.type==='image'" class="box image-box" style="justify-self: flex-start">
                        <div v-if="data.char" :class="[right? 'right':'left']">
                            <div class="tail">
                                <div class="tail2"></div>
                            </div>
                        </div>
                        <img :id="data.id" :key="id" :src="images[data.content]"
                             :style="{width: preScreenshot?width.image:'100%'}"
                        >
                    </div>
                    <template v-else-if="data.type==='chat'">
                        <div v-if="data.char" class="box dialogue-box">
                            <div :class="[right? 'right':'left']">
                                <div class="tail">
                                    <div class="tail2"></div>
                                </div>
                            </div>
                            <pre style="font-family: Harmony">{{data.content}}</pre>
                        </div>
                        <div v-else style="color: #CCCCCC" class="box">
                            <pre>{{data.content}}</pre>
                        </div>
                    </template>
                    <template v-else-if="data.type==='monologue'">
                        <div v-if="data.char" class="box monologue-box">
                            <pre>{{data.content}}</pre>
                        </div>
                        <div v-else style="color: #909090" class="box">
                            <pre>{{data.content}}</pre>
                        </div>
                    </template>
                    <template v-else-if="data.type==='option'">
                        <div class="option-box">
                            <div v-for="value in data.content" class="block">
                                <div class="bg">
                                    <div class="triangle" style="margin-left: 14px"></div>
                                    <div class="triangle"></div>
                                    <div class="triangle"></div>
                                </div>
                                <div class="text">
                                    <pre>{{value[1]}}</pre>
                                </div>
                            </div>
                        </div>
                    </template>
                    <template v-else-if="data.type==='select'">
                        <div class="box select-box">
                            <div class="triangle-box">
                                <div class="triangle" style="margin-left: 14px"></div>
                                <div class="triangle"></div>
                                <div class="triangle"></div>
                            </div>
                            <div class="text">
                                <pre>{{data.content}}</pre>
                            </div>
                            <div class="triangle-box" style="transform: rotate(180deg)">
                                <div class="triangle" style="margin-left: 14px"></div>
                                <div class="triangle"></div>
                                <div class="triangle"></div>
                            </div>
                        </div>
                    </template>
                    <div v-if="charDirection[1]" class="avatar" style="margin-left: 10px">
                        <div v-if="right === true">
                            <img src="/avatar-bg.png">
                            <img :src="images[char.avatar] || staticUrl + char.avatar">
                        </div>
                    </div>
                </template>
            </div>
        </div>
    </div>
</template>

<style src=".global.css"></style>
<style src=".scoped.css" scoped></style>
<style scoped>
    .avatar {
        width: v-bind('width.avatar');
        height: v-bind('width.avatar');
    }

    .dialogue pre {
        font-size: v-bind('width.fontsize');
    }
</style>