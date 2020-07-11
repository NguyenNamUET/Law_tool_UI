<template>
    <section>
        <div>
            <b-button style="margin-right:1.25em" type="is-primary" @click="doSearch">Search</b-button>
            <b-button type="is-primary" @click="mark">Mark</b-button>
        </div>

        <b-table
            :data="data"
            ref="table"
            detailed
            hoverable
            custom-detail-row
            :default-sort="['name', 'asc']"
            detail-key="text"
            :show-detail-icon="showDetailIcon">

            <template slot-scope="props">
                <b-table-column
                    field="name"
                    :visible="columnsVisible['name'].display"
                    :label="columnsVisible['name'].title"
                    width="300"
                    sortable>
                    <template v-if="showDetailIcon">
                        <b>{{ props.row.text.charAt(0).toUpperCase() + props.row.text.slice(1) }}</b>
                    </template>
                    <template v-else>
                        <a @click="toggle(props.row)">
                            <b>{{ props.row.text.charAt(0).toUpperCase() + props.row.text.slice(1) }}</b>
                        </a>
                    </template>
                </b-table-column>
                <b-table-column
                        field="link"
                        :visible="columnsVisible['link'].display"
                        :label="columnsVisible['link'].title"
                        >
                </b-table-column>
                <b-table-column
                        field="issued_date"
                        :visible="columnsVisible['issued_date'].display"
                        :label="columnsVisible['issued_date'].title"
                        sortable>

                </b-table-column>
                <b-table-column
                        field="enforced_date"
                        :visible="columnsVisible['enforced_date'].display"
                        :label="columnsVisible['enforced_date'].title"
                        sortable>
                </b-table-column>
                <b-table-column
                    field="document_field"
                    :visible="columnsVisible['document_field'].display"
                    :label="columnsVisible['document_field'].title"
                    width="200"
                    sortable>
                </b-table-column>
                <b-table-column
                    field="issuing_body"
                    :visible="columnsVisible['issuing_body'].display"
                    :label="columnsVisible['issuing_body'].title"
                    width="200"
                    sortable>
                </b-table-column>
                <b-table-column
                    field="signer"
                    :visible="columnsVisible['signer'].display"
                    :label="columnsVisible['signer'].title"
                    width="300"
                    sortable>
                </b-table-column>
            </template>

            <template slot="detail" slot-scope="props">
                <tr v-for="item in props.row.detailed" :key="item.official_number">
                    <td v-if="showDetailIcon"></td>
                    <td v-show="columnsVisible['name'].display" >&nbsp;&nbsp;&nbsp;&nbsp;{{ item.document_type + " " +item.official_number[0] }}</td>
                    <td v-show="columnsVisible['link'].display">
                        <a :href="item.url">
                            <p>click me!</p>
                        </a>
                    </td>
                    <td v-show="columnsVisible['issued_date'].display">
                        <span class="tag is-success">
                            {{ new Date(item.issued_date).toLocaleDateString() }}
                        </span>
                    </td>
                    <td v-show="columnsVisible['enforced_date'].display">
                        <span class="tag is-warning">
                            {{ new Date(item.enforced_date).toLocaleDateString() }}
                        </span>
                    </td>
                    <td v-show="columnsVisible['document_field'].display">{{ item.document_field[0] }}</td>
                    <td v-show="columnsVisible['issuing_body'].display">{{ item['issuing_body/office/signer'][0] }}</td>
                    <td v-show="columnsVisible['signer'].display">{{ item['issuing_body/office/signer'][2] }}</td>
                </tr>
            </template>
        </b-table>
    </section>

</template>

<script>

    import axios from "axios";
    const path = "http://112.137.142.8:5000/api/lawtech/document/NERsearch"
    export default {
        name: "SearchButton",
        props: ['copied_editorData'],
        data() {
            return {
                data: [],
                columnsVisible: {
                    name: { title: 'Tên văn bản', display: true },
                    link: { title: 'Link văn bản', display: true },
                    issued_date: { title: 'Ngày ban hành', display: true },
                    enforced_date: { title: 'Ngày có hiệu lực', display: true },
                    document_field: { title: 'Lĩnh vực văn bản', display: true },
                    issuing_body: { title: 'Cơ quan ban hành', display: true },
                    signer: { title: 'Người kí', display: true },
                },
                showDetailIcon: true
            }
        },
        methods:{
            async doSearch(){
                let content = document.createElement("textarea");
                content.innerHTML = this.copied_editorData;
                content = content.value.replace(/<p>|<\/p>|<mark>|<\/mark>/g,"")
                console.log(content)
                 try {
                      const response = await axios({
                            url: path,
                            method: 'post',
                            data: {
                                doc_text: content
                            }
                      });
                      if (response.status === 200) {
                          for(var i=0; i<response.data["found"].length; i++){
                              if ((response.data["found"][i]["text"].match(/\s/gi)).length > 1){
                                  this.data.push(response.data["found"][i]);
                              }
                          }
                          //this.data = response.data["found"];
                          console.log(this.data);
                      }
                  } catch (error) {
                    console.log(error)
                  }
            },
            mark(){
                let new_editorData = document.createElement("textarea");
                new_editorData.innerHTML = this.copied_editorData;
                new_editorData = new_editorData.value
                console.log(new_editorData)
                for (var i=0; i<this.data.length; i++){
                    if ((this.data[i]["text"].match(/\s/gi)).length > 1){
                        var find = this.data[i]["text"].replace(/ +/gi,"").split('').join('\\s*');
                        console.log("find: "+find)
                        var re = new RegExp(find, 'gi');
                        console.log("re: "+re)
                        new_editorData = new_editorData.replace(re, "<mark>"+this.data[i]["text"].charAt(0).toUpperCase() + this.data[i]["text"].slice(1)+"</mark>").
                                                    replace(/(<mark>){2,}/g, '<mark>').
                                                    replace(/(<\/mark>){2,}/g, '</mark>')
                    }
                }
                console.log("new_editorData: "+new_editorData)
                this.$emit('update-editor-content', new_editorData)
            },
            toggle(row) {
                this.$refs.table.toggleDetails(row)
            }
        }
    }
</script>

<style scoped>

</style>