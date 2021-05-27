<template>
  <div class="container">
    <!-- Criar tarefa -->
    <div class="row">
      <div class="col-lg-6">
        <button
          type="button"
          class="btn btn-outline-primary"
          data-bs-toggle="modal"
          data-bs-target="#todoModal">
          Criar tarefa
        </button>
      </div>
    </div>

    <!-- Modal -->
    <div
      class="modal fade"
      id="todoModal"
      tabindex="-1"
      aria-labelledby="todoModalLabel"
      aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5
              class="modal-title">
              TODO
            </h5>

            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close">
            </button>
          </div>

          <div class="modal-body">
            <form>
              <div class="form-group mt-4">
                <label>Descrição</label>
                <input
                  type="date"
                  class="form-control"
                  placeholder="Descrição" />
              </div>
            </form>
          </div>

          <div class="modal-footer">
            <button
              ref="closeBtn"
              type="button"
              class="btn btn-secondary"
              data-bs-dismiss="modal">
              Fechar
            </button>

            <button
              @click="addExpense()"
              type="button"
              class="btn btn-primary">
              Guardar
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Listagem de todos -->
    <div class="row mt-4">
      <div class="col-lg-12">
        <table class="table table-striped">
          <thead>
            <tr>
              <th scope="col">#</th>
              <th scope="col">Id do Utilizador</th>
              <th scope="col">Descrição</th>
              <th scope="col">Estado</th>
              <th scope="col">Criado a</th>
              <th scope="col">Atualizado a</th>
            </tr>
          </thead>

          <tbody>
            <tr
              v-if="!hasTodos">
              <td
                colspan="6">
                Não existem tarefas criadas!
              </td>
            </tr>
            <tr
              v-else
              v-for="todo in todos"
              :key="todo.id">
              <td>{{ todo.id }}</td>
              <td>{{ todo.user_id }}</td>
              <td>{{ todo.title }}</td>
              <td>
                <i
                  v-if="todo.completed"
                  class="fas fa-check text-success">
                </i>
                <i
                  v-else
                  class="fas fa-times text-danger">
                </i>
              </td>
              <td>{{ formatDate(todo.created_at) }}</td>
              <td>{{ formatDate(todo.updated_at) }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <div class="row mt-4">
      <nav aria-label="Page navigation example">
        <ul class="pagination">
          <li class="page-item">
            <a
              @click.prevent="previousPage()"
              class="page-link"
              :class="{'disabled': pagination.page == 1}"
              href="#">
              Página Anterior
            </a>
          </li>

          <li class="page-item">
            <a class="page-link" href="#">{{ pagination.page }}</a>
          </li>

          <li class="page-item">
            <a
              @click.prevent="nextPage()"
              :class="{'disabled': pagination.page == pagination.pages}"
              class="page-link"
              href="#">
              Página Seguinte
            </a>
          </li>
        </ul>
      </nav>
    </div>
  </div>
</template>
<script>
import moment from 'moment'

export default {
  name: 'Todos',

  components: {
  },

  computed: {
    hasTodos () {
      return this.todos.length > 0
    }
  },

  data () {
    return {
      maintenanceTodo: {
        id: null,
        userId: null,
        title: '',
        completed: false
      },

      todos: [],

      pagination: {
        total: null,
        pages: null,
        page: 1,
        limit: null
      }
    }
  },

  methods: {
    getTodos () {
      this.axios.get('https://gorest.co.in/public-api/todos?page=' + this.pagination.page).then((response) => {
        this.todos = response.data.data
        this.pagination = response.data.meta.pagination
      })
    },

    formatDate (date) {
      var splitedDate = date.split('.')[0]

      return moment(splitedDate, 'YYYY-MM-DDTHH:mm:ss').format('DD/MM/YYYY HH:mm:ss')
    },

    previousPage () {
      if (this.pagination.page > 1) {
        this.pagination.page--
      }

      this.getTodos()
    },

    nextPage () {
      if (this.pagination.page < this.pagination.pages) {
        this.pagination.page++
      }

      this.getTodos()
    }
  },

  created () {
    this.getTodos()
  }
}
</script>

<style>
  .pagination {
    justify-content: center;
  }

  .disabled {
    color: currentColor;
    cursor: not-allowed;
    opacity: 0.5;
    text-decoration: none;
  }
</style>
