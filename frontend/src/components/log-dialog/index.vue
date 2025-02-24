<template>
    <el-drawer
        v-model="open"
        :destroy-on-close="true"
        :close-on-click-modal="false"
        :size="globalStore.isFullScreen ? '100%' : '50%'"
    >
        <template #header>
            <DrawerHeader :header="$t('website.log')" :back="handleClose">
                <template #extra v-if="!mobile">
                    <el-tooltip :content="loadTooltip()" placement="top">
                        <el-button @click="toggleFullscreen" class="fullScreen" icon="FullScreen" plain></el-button>
                    </el-tooltip>
                </template>
            </DrawerHeader>
        </template>
        <div>
            <LogFile :config="config"></LogFile>
        </div>
    </el-drawer>
</template>
<script lang="ts" setup>
import { computed, ref } from 'vue';
import LogFile from '@/components/log-file/index.vue';
import { GlobalStore } from '@/store';
import screenfull from 'screenfull';
import i18n from '@/lang';

const globalStore = GlobalStore();

const mobile = computed(() => {
    return globalStore.isMobile();
});

interface LogProps {
    id: number;
    type: string;
    style: string;
    name: string;
}

const open = ref(false);
const config = ref();
const em = defineEmits(['close']);

const handleClose = (search: boolean) => {
    open.value = false;
    em('close', search);
};

function toggleFullscreen() {
    if (screenfull.isEnabled) {
        screenfull.toggle();
    }
}

const loadTooltip = () => {
    return i18n.global.t('commons.button.' + (screenfull.isFullscreen ? 'quitFullscreen' : 'fullscreen'));
};

const acceptParams = (props: LogProps) => {
    config.value = props;
    open.value = true;

    if (!mobile.value) {
        screenfull.on('change', () => {
            globalStore.isFullScreen = screenfull.isFullscreen;
        });
    }
};

defineExpose({ acceptParams });
</script>

<style lang="scss">
.fullScreen {
    border: none;
}
</style>
