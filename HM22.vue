<template>
  <div>
    <div class="container mt-4">
      <div class="row">
        <div class="col-12">
          <button type="button" class="btn btn-success mb-3" @click="create()">Add</button>

          <ul id="todo-list" class="list-group">
            <li
                v-for="todoItem in todoList"
                :key="todoItem.uuid"
                class="list-group-item d-flex justify-content-between align-items-center"
            >
              <div>
                <input class="form-check-input" type="checkbox" :checked="todoItem.completed" @click="toggleComplete(todoItem.uuid)">
                {{ todoItem.title }}
              </div>
              <div><small>{{ todoItem.description }}</small></div>

              <div>
                <button class="btn btn-warning btn-sm" @click="edit(todoItem.uuid)">Edit</button>
                <button class="btn btn-danger btn-sm" @click="confirmRemove(todoItem.uuid)">Remove</button>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>

    <div id="form-modal" class="modal d-block" v-if="showFormModal">
      <div class="modal-dialog">
        <div class="modal-content">
          <form id="form" action="#" method="post">
            <div class="modal-body">
              <input id="form-uuid" type="hidden">

              <label for="form-title" class="form-label">Title</label>
              <input type="text" id="form-title" name="title" class="form-control" v-model="filledTodoItem.title">
              <div id="form-title-invalid-feedback" class="invalid-feedback"></div>

              <textarea class="form-control mt-2" placeholder="Description..." v-model="filledTodoItem.description"></textarea>

              <div class="form-check mt-2">
                <input class="form-check-input" type="checkbox" v-model="filledTodoItem.completed">
                <label class="form-check-label" for="form-completed">
                  Completed
                </label>
              </div>

              <pre>{{filledTodoItem}}</pre>
              <pre>{{todoList}}</pre>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" @click="showFormModal = false">Close</button>
              <button type="button" class="btn btn-success" @click="save()">Save</button>
            </div>
          </form>
        </div>
      </div>
    </div>

    <div id="remove-confirmation-modal" class="modal d-block" v-if="showRemoveConfirmationModal">
      <div class="modal-dialog">
        <div class="modal-content">
          <form id="form" action="#" method="post">
            <div class="modal-body">
              Are you sure?
              This item "{{ deletedItem.title }}" will be deleted.
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" @click="showRemoveConfirmationModal = false">Cancel</button>
              <button type="button" class="btn btn-success" @click="remove()">Delete</button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      todoList: [],
      filledTodoItem: {
        uuid: null,
        title: null,
        description: null,
        completed: false,
      },
      deletedItem: {},
      showFormModal: false,
      showRemoveConfirmationModal: false,
    };
  },
  methods: {
    create() {
      this.filledTodoItem = {
        uuid: null,
        title: '',
        description: '',
        completed: false,
      };
      this.showFormModal = true;
    },
    edit(uuid) {
      let editableItem = this.todoList.find((item) => item.uuid === uuid);
      this.filledTodoItem = { ...editableItem };
      this.showFormModal = true;
    },
    confirmRemove(uuid) {
      this.deletedItem = this.todoList.find((item) => item.uuid === uuid);
      this.showRemoveConfirmationModal = true;
    },
    save() {
      this.filledTodoItem.uuid = this.filledTodoItem.uuid ? this.filledTodoItem.uuid : this.generateUuid();
      let index = this.todoList.findIndex((item) => item.uuid === this.filledTodoItem.uuid);
      if (index === -1) {
        this.todoList.push(this.filledTodoItem);
      } else {
        this.todoList[index] = { ...this.filledTodoItem };
      }
      this.showFormModal = false;
    },
    remove() {
      let index = this.todoList.findIndex((item) => item.uuid === this.deletedItem.uuid);
      if (index !== -1) {
        this.todoList.splice(index, 1);
      }
      this.showRemoveConfirmationModal = false;
    },
    generateUuid() {
      return Math.random().toString(16).slice(2);
    },
    toggleComplete(uuid) {
      let item = this.todoList.find((item) => item.uuid === uuid);
      if (item) {
        item.completed = !item.completed;
      }
    },
  },
};
</script>
