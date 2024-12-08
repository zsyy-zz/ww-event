<script setup>
import { artGetChannelsService } from '@/api/article'
import { ref } from 'vue'

defineProps({
    modelValue:{
        type:[Number, String]
    },
    width:{
        type: String
    }
})

const emit = defineEmits(['update:modelValue'])

const channelLIst = ref([])
const getChanneList = async () => {
    const res = await artGetChannelsService()
    channelLIst.value = res.data.data
    console.log(channelLIst.value)
}
getChanneList()
</script>

<template>
 <!-- label展示给用户 value给后台 -->
    <el-select 
    style="width:200px"
    :modelValue="modelValue" 
    @update:modelValue="emit('update:modelValue',$event)"
    :style="{ width }"
    >
        <el-option 
        v-for="channel in channelLIst" 
        :key="channel.id"
        :label="channel.cate_name" 
        :value="channel.id"
        ></el-option>
    </el-select>
</template>