<template>
    <b-container>
        <br>
        <b-row>
            <b-table hover head-variant="light" small :busy="loading" striped caption-top
                     :items="items" responsive :fields="fields" class="text-left" show-empty>
                <template v-slot:table-caption>
                    <b-row class="mr-0 ml-0">
                        <b-col sm=6 md="8" lg="8">
                            <span class="h5">User List</span>
                        </b-col>
                        <b-col sm="6" md="4" lg="4">
                            <b-row align-h="end">
                                <b-button variant="primary" class="mr-2" @click="show_model=!show_model">Add User
                                </b-button>
                                <b-button pill variant="info" @click="getData()">
                                    <b-icon icon="arrow-clockwise" :animation="loading ? 'spin': ''"></b-icon>
                                </b-button>
                            </b-row>
                        </b-col>
                    </b-row>
                </template>
                <template v-slot:empty="scope">
                    <h6 class="text-center" style="color:#a80003">{{ scope.emptyText }}</h6>
                </template>
                <template v-slot:cell(extra_phone)="data">
                    <b-button variant="link" v-if="data.item.extra_phone && data.item.extra_phone.length>0"
                              @click="extra_no=data.item.extra_phone;model2=!model2">view extra No
                    </b-button>
                </template>
                <template v-slot:table-busy>
                    <div class="text-center text-primary my-2">
                        <b-spinner class="align-middle"></b-spinner>
                        <strong> Loading...</strong>
                    </div>
                </template>
            </b-table>
        </b-row>
        <add_user :modalShow="show_model" @callList="getData()"></add_user>
        <b-modal id="modal2" v-model="model2" title="Extra No" ok-only>
            <b-row v-for="(nos, index) in extra_no" :key="index">
                <b-col>
                    Mobile No {{index+1}} -
                    {{nos.mobile_no}}
                </b-col>
            </b-row>
        </b-modal>
    </b-container>
</template>

<script>
  import add_user from "./add_user";

  export default {
    name: "User_view",
    data() {
      return {
        model2: false,
        show_model: false,
        loading: false,
        user_data: [],
        extra_no: [],
        fields: [
          {
            key: 'username',
            sortable: false,
            label: 'User Name',
          },
          {
            key: 'first_name',
            sortable: false,
            label: 'First Name'
          },
          {
            key: 'last_name',
            sortable: false,
            label: 'Last Name'
          },
          {
            key: 'email',
            label: 'Email',
            sortable: false,
          },
          {
            key: 'date_of_birth',
            label: 'Date of Birth',
            sortable: false,
            stickyColumn: false,
          },
          {
            key: 'mobile_no',
            label: 'Mobile No'
          },
          {
            key: 'extra_phone',
            label: 'Extra Mobile No'
          }
        ],
        items: [],
      }
    },
    created() {
      this.getData();
    },
    methods: {
      getData() {
        this.loading = true;
        this.axios.get('api/users').then(response => {
          if (response) {
            this.items = response.data;
            this.loading = false;
          }
        }).catch((error) => {
          this.loading = false;
          this.$bvToast.toast(`${JSON.stringify(error.response.data)}`, {
            title: 'Failed',
            variant: 'danger',
            autoHideDelay: 4000,
          });
        });
      },
    },
    components: {
      add_user
    }
  }
</script>

<style scoped>

</style>