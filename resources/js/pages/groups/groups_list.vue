


<template>
  <div v-if="isLoadig">
    <Breadcrumbs :main="$t('Dashboard')" :title="$t('groups')" />
    <div class="container-fluid">
      <div class="row">
        <div class="col-md-12">
          <div class="card">
            <div class="card-body">
              <div class="datatable-vue m-0">
                <div class="table-responsive vue-smart">
                  <b-table
                    id="tablePrint"
                    show-empty
                    stacked="md"
                    :items="groups"
                    :fields="tablefields"
                    :current-page="currentPage"
                    :per-page="perPage"
                  >
                    <template #cell(id)="data">
                      {{ ++data.index }}
                    </template>

                    <template #cell(action)="data">
                      <div>
                        <b-button-group class="btn-container">
                          <b-button
                            squared
                            variant="outline-primary"
                            class="btn-sm btn-child"
                            v-if="can('edit-group')"
                          >
                            <router-link
                              :to="{
                                name: 'updateGroup',
                                params: { groupId: data.item.id },
                              }"
                            >
                              {{ $t("edit") }}
                            </router-link>
                          </b-button>
                          <b-button
                            squared
                            variant="outline-danger"
                            class="btn-sm btn-child"
                            @click="DeletegroupModal(data.item.id, data.index)"
                            v-if="can('delete-group')"
                          >
                            {{ $t("delete") }}
                          </b-button>
                        </b-button-group>
                      </div>
                    </template>
                  </b-table>
                </div>

                <b-col md="6" class="my-1">
                  <b-pagination
                    v-model="currentPage"
                    :total-rows="totalRows"
                    :per-page="perPage"
                    class="my-0"
                  ></b-pagination>
                </b-col>
              </div>

              <b-modal id="bv-modal-example" hide-footer>
                <template #modal-title>
                  {{ $t("Delete group") }}
                </template>
                <div class="d-block text-center">
                  <h5>{{ $t("are you sure to delete this group") }}</h5>
                </div>
                <b-button
                  class="mt-3"
                  v-b-modal.modal-sm
                  variant="default"
                  @click="$bvModal.hide('bv-modal-example')"
                  >{{ $t("Cancel") }}</b-button
                >
                <b-button
                  class="mt-3"
                  v-b-modal.modal-sm
                  variant="danger"
                  @click="deletegroup"
                  >{{ $t("delete") }}</b-button
                >
              </b-modal>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div
    v-else
    class="col-md-3"
    style="
      margin: auto;
      position: absolute;
      top: 50%;
      right: 50%;
      transform: translate(50%, -50%);
    "
  >
    <h6 class="sub-title mb-0 text-center"></h6>
    <div class="loader-box">
      <div class="loader-3"></div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      tablefields: [
        { id: this.$t("id") },
        { key: "name", label: this.$t("group name"), sortable: false },
        { action: this.$t("action") },
      ],
      totalRows: 1,
      currentPage: 1,
      perPage: 10,
      pageOptions: [5, 10, 15],

      groups: [],

      filter: {
        currentPage: 1,
        totalPages: 0,
      },

      id: "",
      key: "",
      isLoadig: false,
    };
  },
  beforeMount() {
    axios
      .get("groups")
      .then((res) => {
        this.groups = res.data.groups;
        this.totalRows = res.data.groups.lenght;
        this.isLoadig = true;
      })
      .then((err) => {});
  },
  mounted() {},
  methods: {
    DeletegroupModal(id, key) {
      this.id = id;
      this.key = key;
      this.$bvModal.show("bv-modal-example");
    },

    deletegroup() {
      axios
        .get(`deleteGroup/${this.id}`)
        .then((res) => {
          if (res.data.success) {
            this.groups.splice(this.key, 1);
            this.id = "";
            this.key = "";
            Toast.fire({
              icon: "success",
              title: this.$t("group deleted successfully"),
            });

            this.$bvModal.hide("bv-modal-example");
          }
        })
        .catch((err) => {});
    },
  },
};
</script>
<style lang="scss">
.btn-container {
  .btn-child {
    padding: 0.2rem 0.8rem !important;
  }
}
.custom-color {
  color: inherit;
  padding: 0;
  margin: 0;
  border-radius: 0;
  margin-left: -1px;
  &:hover {
    color: inherit;
  }
}
</style>
