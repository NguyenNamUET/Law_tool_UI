<template>
    <div id="Editor">
        <ckeditor type="classic" v-model="editorData" :config="editorConfig"></ckeditor>
        <SearchButton v-bind:copied_editorData="editorData" @update-editor-content="updateEditorContent"></SearchButton>
    </div>
</template>

<script>
    import SearchButton from "./SearchButton"
    export default {
        name: "Editor",
        components:{
            SearchButton
        },
        data() {
            return {
                editorData: "",
                editorConfig: {
                    allowedContent: 'p; mark'
                }
            };
        },
        methods:{
            updateEditorContent(content){
                this.editorData = content
            }
        }
    }
</script>