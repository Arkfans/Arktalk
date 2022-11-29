<script setup>
    import {ref, inject, computed, watch, onMounted, nextTick} from 'vue';
    import {uuid} from '@/lib/tool'

    const chars = inject('chars');
    const images = inject('images');
    const settings = inject('settings');
    const width = inject('width');
    const charDirection = inject('charDirection');
    const {data, index} = defineProps(['data', 'index']);
    defineEmits(['edit']);
    const char = computed(() => {
        return chars.value[data.char] || {};
    });
    const id = ref(uuid());

    const right = computed(() => {
        if (data.char) {
            // opposite is deprecated
            // return data.opposite ? !char.value.right : !!char.value.right
            return !!char.value.right
        } else {
            return null
        }
    });

    function resize(t) {
        // 当height为小数时，html2canvas 会有1px的误差，在此赋值为整数
        if (data.type === 'image') {
            const el = document.getElementById(data.id);
            if (el.offsetHeight === 0) {
                if (t > 10) {
                    return
                }
                if (!t) {
                    t = 1
                }
                setTimeout(resize, 5, t++)
            } else {
                el.style.height = el.offsetHeight + 'px'
            }
        }
    }

    watch(data, () => {
        nextTick(resize)
    });
    onMounted(() => {
        resize()
    });

    watch(charDirection, () => {
        id.value = uuid();
    })
</script>

<template>
    <div :class="settings.style">
        <div class="dialogue">
            <div style="display: flex; width: 100%; margin-bottom: 10px">
                <div v-if="charDirection[0]" class="avatar" @click="$emit('edit', index)">
                    <div v-if="right === false">
                        <img src="/avatar-bg.png">
                        <img :src="images[char.avatar] || char.avatar">
                    </div>
                </div>
                <div v-if="data.type==='image'" class="image-box">
                    <div v-if="data.char" :class="[right? 'right':'left']">
                        <div class="tail">
                            <div class="tail2"></div>
                        </div>
                    </div>
                    <img :id="data.id" :key="id" :src="images[data.content]">
                </div>
                <template v-if="data.type==='chat'">
                    <div v-if="data.char" class="dialogue-box">
                        <div :class="[right? 'right':'left']">
                            <div class="tail">
                                <div class="tail2"></div>
                            </div>
                        </div>
                        <pre style="font-family: Harmony">{{data.content}}</pre>
                    </div>
                    <div v-else style="color: #CCCCCC" class="narration-box">
                        <pre>{{data.content}}</pre>
                    </div>
                </template>
                <template v-if="data.type==='monologue'">
                    <div v-if="data.char" class="monologue-box">
                        <pre>{{data.content}}</pre>
                    </div>
                    <div v-else style="color: #909090" class="narration-box">
                        <pre>{{data.content}}</pre>
                    </div>
                </template>
                <div v-if="charDirection[1]" class="avatar" @click="$emit('edit', index)">
                    <div v-if="right === true">
                        <img src="/avatar-bg.png">
                        <img :src="images[char.avatar] || char.avatar">
                    </div>
                </div>
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