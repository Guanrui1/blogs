<template>
    <ParentLayout>
        <template #page-bottom>
            <PageBottom />
        </template>
    </ParentLayout>
</template>

<script>
import ParentLayout from '@parent-theme/layouts/Layout.vue'
import PageBottom from '@theme/components/PageBottom.vue'

import addWhiteMaskToNav from '../mixins/addWhiteMaskToNav'
import catchSidebarScroll from '../mixins/catchSidebarScroll'
import useConfetti from '../mixins/useConfetti'
import useCrisp from '../mixins/useCrisp'
import useCSSBridge from '../mixins/useCSSBridge'

const utils = require('../utils')

export default {
    name: 'Layout',
    components: {
        ParentLayout,
        PageBottom
    },
    mixins: [addWhiteMaskToNav, catchSidebarScroll, useConfetti, useCrisp, useCSSBridge],
    mounted() {
        window.onload = utils.onload.fn
    },
    beforeDestroy() {
        utils.onload.destroy()
    }
}
</script>
