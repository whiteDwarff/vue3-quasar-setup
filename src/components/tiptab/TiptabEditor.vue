<template>
  <TiptabEditorMenu :editor="editor" />
  <q-separator />
  <editor-content :editor="editor" class="editor__content" />
</template>

<script setup>
import { watch } from "vue";
import { useEditor, EditorContent } from "@tiptap/vue-3";
import StarterKit from "@tiptap/starter-kit";
import Placeholder from "@tiptap/extension-placeholder";
import Link from "@tiptap/extension-link";
import Image from "@tiptap/extension-image";

import TiptabEditorMenu from "./tibtabEditorMenu.vue";

// 상위 컴포넌트에서 v-model 사용
const props = defineProps({
  modelValue: {
    type: String,
    default: "",
  },
});
const emit = defineEmits(["update:modelValue"]);

const editor = useEditor({
  content: props.modelValue,
  extensions: [
    StarterKit,
    Placeholder.configure({
      placeholder: "add contents here",
    }),
    Link,
    Image,
  ],
  // editor에 변화가 일어났을 때 ~
  // 사용자의 입력 등...
  onUpdate: () => {
    emit("update:modelValue", editor.value.getHTML());
  },
});

watch(
  () => props.modelValue,
  (value) => {
    const isSame = editor.value.getHTML() === value;
    // 외부에서 사용자의 입력등으로 content가 변화되었지만 값이 같은 경우
    if (isSame) return;
    // 값이 다른 경우
    editor.value.commands.setContent(value, false);
  },
);
</script>

<style>
.tiptap p.is-editor-empty:first-child::before {
  color: #adb5bd;
  content: attr(data-placeholder);
  float: left;
  height: 0;
  pointer-events: none;
}
</style>
<style lang="scss" src="src/css/tibtab.scss"></style>
<!-- 
❗️ component 호출 
import TiptabEditor from 'src/components/tiptab/TiptabEditor.vue';

❗️ component 사용
  - tiptab이 <form> 요새에 있는 경우 @submit.prevent 필수
 
-----------------------------------------------------------------
  <template>
    <q-card class="tiptap" flat bordered>
      ...
      <TiptabEditor v-model="content" />
      ...
    </q-card>
  </template>
  <script setup>
  import { ref } from 'vue';
  const content = ref('');
  </script>
  <style lang="scss" src="src/css/tibtab.scss"></style>
-----------------------------------------------------------------
 -->
