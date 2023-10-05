<template>
  <div id="mangeQuestionView"></div>
  <a-table
    :columns="columns"
    :data="dataList"
    :pagination="{
      showTotal: true,
      pageSize: searchParms.pageSize,
      current: searchParms.current,
      total,
    }"
    @page-change="onPageChange"
  >
    <template #optional="{ record }">
      <a-space>
        <a-button type="primary" @click="doUpdate(record)">修改</a-button>
        <a-button status="danger" @click="doDelete(record)">删除</a-button>
      </a-space>
    </template>
  </a-table>
</template>

<script setup lang="ts">
import { onMounted, ref, watchEffect } from "vue";
import { Question, QuestionControllerService } from "../../../generated";
import message from "@arco-design/web-vue/es/message";
import { useRouter } from "vue-router";

const show = ref(true);
const dataList = ref([]);
const total = ref(0);
const searchParms = ref({
  pageSize: 10,
  //current是当前页号
  current: 1,
});

const loDada = async () => {
  const res = await QuestionControllerService.listQuestionByPageUsingPost(
    searchParms.value
  );
  if (res.code === 0) {
    dataList.value = res.data.records;
    total.value = res.data.total;
  } else {
    message.error("加载失败" + res.message);
  }
};
/**
 * 监听 searchParams 变量，改变时触发页面的重新加载
 */
watchEffect(() => {
  loDada();
});
/**
 * 页面加载时请求数据
 * {id: "1", title: "A+B", content: "题目内容", tags: "["栈","简单"]", answer: "暴力破解", submitNum: 0,…}
 */
onMounted(() => {
  loDada();
});

const columns = [
  {
    title: "id",
    dataIndex: "id",
  },
  {
    title: "标题",
    dataIndex: "title",
  },
  {
    title: "内容",
    dataIndex: "content",
  },
  {
    title: "标签",
    dataIndex: "tags",
  },
  {
    title: "答案",
    dataIndex: "answer",
  },
  {
    title: "提交数",
    dataIndex: "submitNum",
  },
  {
    title: "判题配置",
    dataIndex: "judgeConfig",
  },
  {
    title: "判题用例",
    dataIndex: "judgeCase",
  },
  {
    title: "通过数",
    dataIndex: "acceptedNum",
  },
  {
    title: "用户ID",
    dataIndex: "userId",
  },
  {
    title: "创建时间",
    dataIndex: "createTime",
  },
  {
    title: "操作",
    slotName: "optional",
  },
];
const router = useRouter();
const doUpdate = async (question: Question) => {
  router.push({
    path: "/update/question",
    query: {
      id: question.id,
    },
  });
};
const onPageChange = (page: number) => {
  searchParms.value = {
    ...searchParms.value,
    current: page,
  };
};
const doDelete = async (question: Question) => {
  const res = await QuestionControllerService.deleteQuestionUsingPost({
    id: question.id,
  });
  if (res.code === 0) {
    message.success("删除成功");
    //更新数据
    loDada();
  }
};
</script>
