<script setup>
import { onMounted, useTemplateRef } from "vue";
import UndoIcon from "@/icons/UndoIcon.vue";
import RedoIcon from "@/icons/RedoIcon.vue";
import HeadingIcon from "@/icons/HeadingIcon.vue";
import ParagraphIcon from "@/icons/ParagraphIcon.vue";
import ImageIcon from "@/icons/ImageIcon.vue";

const content = useTemplateRef("content");

function performAction(command, argument = null) {
    if (command === "insertImage") {
        let url = prompt("Введите ссылку на изображение");
        document.execCommand("insertImage", false, url);
        return;
    }

    document.execCommand(command, false, argument);
}

function copyContent() {
    const html = content.value.innerHTML;
    navigator.clipboard.writeText(html)
        .then(() => {
            alert("Скопировано");
        })
        .catch((error) => {
            alert(`Текст не скопирован ${error}`);
        });
}

function escapeText(text) {
    const map = { '&': '&amp;', '<': '&lt;', '>': '&gt;', '"': '&quot;', "'": '&#039;' };
    return text.replace(/[&<>"']/g, (m) => {
        return map[m];
    });
}

function pasteListener(e) {
    e.preventDefault();
    const text = (e.originalEvent || e).clipboardData.getData("text/plain");
    document.execCommand("insertHtml", false, escapeText(text));
}

onMounted(() => {
    content.value.focus();
});
</script>

<template>
    <div class="editor">
        <div class="editor--nav">
            <button class="editor--action editor--action-icon" @click="performAction('undo')">
                <UndoIcon />
            </button>
            <button class="editor--action editor--action-icon" @click="performAction('redo')">
                <RedoIcon />
            </button>
            <button class="editor--action editor--action-icon" @click="performAction('formatBlock', 'h3')">
                <HeadingIcon />
            </button>
            <button class="editor--action editor--action-icon" @click="performAction('formatBlock', 'p')">
                <ParagraphIcon />
            </button>
            <button class="editor--action editor--action-icon" @click="performAction('insertImage')">
                <ImageIcon />
            </button>
            <button class="editor--action editor--action-text" @click="copyContent">
                <span>
                    Скопировать HTML
                </span>
            </button>
        </div>
        <div class="editor--content" contenteditable="true" ref="content" @paste="pasteListener">
        </div>
    </div>
</template>

<style>
.editor {
    max-width: 45rem;
    margin: 0 auto;
}

.editor--nav {
    display: flex;
    align-items: center;
    gap: 0.75rem;
    margin-bottom: 2rem;
}

.editor--content {
    color: white;
}

.editor--action {
    position: relative;
    display: block;
    cursor: pointer;
    user-select: none;
}

.editor--action-icon {
    width: 42px;
    height: 38px;
    border-radius: 4px;
    background-color: #282828;
}

.editor--action-icon svg {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

.editor--action-icon path {
    fill: #639EFF;
}

.editor--action-text {
    height: 38px;
}

.editor--action-text span {
    font-weight: bold;
    color: #639EFF;
    display: block;
    padding: 0 10px;
}

.editor--content h3 {
    font-size: 2rem;
    font-weight: bold;
    display: block;
    margin: 2rem 0;
}

.editor--content p {
    font-size: 1.25rem;
    display: block;
    margin: 1.25rem 0;
}

.editor--content img {
    margin: 2rem 0;
}
</style>
