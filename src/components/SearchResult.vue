<template>
    <div>
        <p>{{this.data1}}</p>
        <b-table
                :data="data1"
                ref="table"
                paginated
                per-page="5"
                :opened-detailed="defaultOpenedDetails"
                detailed
                :show-detail-icon="showDetailIcon"
                aria-next-label="Next page"
                aria-previous-label="Previous page"
                aria-page-label="Page"
                aria-current-label="Current page">
            <template slot-scope="props">
                <b-table-column field="id" label="ID" width="40" numeric>
                    {{ props.row.id }}
                </b-table-column>

                <b-table-column field="found.name" label="Name" sortable>
                    <template v-if="showDetailIcon">
                        {{ props.row.found.name }}
                    </template>
                    <template v-else>
                        <a @click="toggle(props.row)">
                            {{ props.row.found.name }}
                        </a>
                    </template>
                </b-table-column>
            </template>
            <template slot="detail">
                <b-table
                :data="data"
                bordered
                narrowed
                hoverable>
                    <template>
                      <b-table-column field="RoomID" label="So ki hieu" width="100">
                        12
                      </b-table-column>
                      <b-table-column field="RoomName" label="Ngay ban hanh" width="100">
                        12/03/2020
                      </b-table-column>
                      <b-table-column field="Maxcapacity" label="Nguoi ban hanh" width="100">
                        Kono Dio da
                      </b-table-column>

                    </template>
                </b-table>
            </template>
        </b-table>
    </div>

</template>

<script>
    const data =  [{"id":1,"user":{"first_name":"Jesse","last_name":"Simmons"},"date":"2016/10/15 13:43:27","gender":"Male"},
{"id":2,"user":{"first_name":"John","last_name":"Jacobs"},"date":"2016/12/15 06:00:53","gender":"Male"}]

    export default {
        name: "SearchResult",
        data() {
            return {
                data,
                data1: [],
                defaultOpenedDetails: [1],
                showDetailIcon: true
            }
        },
        updated(){
            for (var i=0; i<this.$store.state.result["updated_result"].length; i++){
                this.data1.push({'id':i, 'found':{'name':this.$store.state.result["updated_result"][i][1]}})
            }
        },
        methods: {

            toggle(row) {
                this.$refs.table.toggleDetails(row)
            },
            updateResultTable(result){
                this.data1 = result
            }
        }
    }
</script>