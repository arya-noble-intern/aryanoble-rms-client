<template>
  <div>
    <div class="header bg-default pb-6">
      <div class="container-fluid">
        <div class="header-body">
          <div class="row align-items-center py-4">
            <div class="col-lg-6 col-4">
              <h6 class="h2 text-white d-inline-block mb-0">
                Employee Request Form
              </h6>
              <p class="text-white">
                These are your submitted ERFs.
              </p>
              <ul class="text-white">
                <li>Add new ERF to request new employee.</li>
                <li>Details button to see your ERF details.</li>
              </ul>
            </div>
            <div class="col-lg-6 col-8 text-right">
              <nuxt-link to="/div/erf/create">
                <button class="btn btn-icon btn-primary" type="button">
                  <span class="btn-inner--icon"
                    ><i class="ni ni-archive-2"></i
                  ></span>
                  <span class="btn-inner--text">Add New ERF</span>
                </button>
              </nuxt-link>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="container-fluid mt--5">
      <div class="row">
        <div class="col-12">
          <div class="card">
            <div class="card-header">
              <h1>Your Latest Submitted ERFs</h1>
            </div>
            <div v-if="ERFS.data" class="table-responsive mb-5">
              <table class="table align-items-center table-white table-hover">
                <thead class="bg-gradient-gray text-white">
                  <tr>
                    <th scope="col" class="sort">
                      Title
                    </th>
                    <th scope="col" class="sort">
                      Job Title
                    </th>
                    <th scope="col" class="sort">
                      Division
                    </th>
                    <th scope="col" class="sort">
                      Department
                    </th>
                    <th scope="col" class="sort"></th>
                  </tr>
                </thead>
                <tbody class="list">
                  <tr v-for="erf in ERFS.data" :key="erf.id">
                    <td>
                      {{ erf.title }}
                      <span
                        v-if="!erf.acceptance"
                        class="badge badge-lg badge-warning"
                        >PENDING</span
                      >
                      <span
                        v-else-if="erf.acceptance.acceptance == 1"
                        class="badge badge-lg badge-info"
                        >LEADER ACCEPTED</span
                      >
                      <span
                        v-else-if="erf.acceptance.acceptance == 2"
                        class="badge badge-lg badge-info"
                        >PIC ACCEPTED</span
                      >
                      <span
                        v-else-if="erf.acceptance.acceptance == 0"
                        class="badge badge-lg badge-danger"
                        >REJECTED BY LEADER</span
                      >
                      <span
                        v-else-if="erf.acceptance.acceptance == 100"
                        class="badge badge-lg badge-danger"
                        >REJECTED BY PIC</span
                      >
                    </td>
                    <td>
                      {{ erf.job_title }}
                    </td>
                    <td>
                      {{ erf.division }}
                    </td>
                    <td>
                      {{ erf.department }}
                    </td>
                    <td>
                      <nuxt-link :to="`/div/erf/${erf.id}`">
                        <button
                          type="button"
                          class="btn btn-primary float-right"
                        >
                          Details
                        </button>
                      </nuxt-link>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
            <div v-else class="col my-4 mx-4">
              <content-placeholders :rounded="true">
                <content-placeholders-heading />
                <content-placeholders-text :lines="5" />
              </content-placeholders>
            </div>
            <div v-if="ERFS.links" class="card-footer">
              <div ref="loadingContainer"></div>
              <nav aria-label="Page navigation example">
                <ul class="pagination justify-content-end">
                  <li class="page-item">
                    <a
                      v-if="ERFS.links.prev && !loading"
                      class="page-link"
                      href="#!"
                      @click="
                        changePage(prev)
                        getERFS(page)
                      "
                    >
                      <i class="fa fa-angle-left"></i>
                      <span class="sr-only">Previous</span>
                    </a>
                  </li>
                  <li class="page-item">
                    <a class="page-link">{{ page }}</a>
                  </li>
                  <li class="page-item">
                    <a
                      v-if="ERFS.links.next && !loading"
                      class="page-link"
                      href="#!"
                      @click="
                        changePage(next)
                        getERFS(page)
                      "
                    >
                      <i class="fa fa-angle-right"></i>
                      <span class="sr-only">Next</span>
                    </a>
                  </li>
                </ul>
              </nav>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters, mapActions } from 'vuex'
export default {
  middleware: ['auth', 'div'],
  name: 'DivERF',
  data() {
    return {
      page: 1,
      file: '',
      readonly: true,
      next: 'next',
      prev: 'prev',
      errors: false,
      loading: false
    }
  },
  computed: {
    ...mapGetters({
      ERFS: 'erfs/ERFS'
    })
  },
  created() {
    this.getERFS(this.page)
  },
  methods: {
    ...mapActions({
      GET_ERFS: 'erfs/GET_ERFS'
    }),
    clearForm() {
      Object.assign(this.$data, this.$options.data())
    },

    async getERFS(page) {
      this.loading = true
      const loader = this.$loading.show({
        container: this.$refs.loadingContainer
      })
      try {
        await this.GET_ERFS(page)
      } catch (e) {
        this.page--
        alert(e.response.data)
      } finally {
        this.loading = false
        loader.hide()
      }
    },
    changePage(change) {
      change === 'next' ? this.page++ : this.page--
    },
    setReadonly() {
      this.readonly = true
    }
  }
}
</script>

<style></style>
