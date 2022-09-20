<template lang="">
  <li class="list-group-item">
    <div class="d-flex justify-content-between align-items-center">
      <div v-if="!showEditInput">
        <input
          class="form-check-input me-1"
          type="checkbox"
          value=""
          id="firstCheckbox"
          @change="changeStatus"
          :checked="item.done"
        />
        <label class="form-check-label" for="firstCheckbox">{{
          item.name
        }}</label>
      </div>
      <div v-else>
        <input
          @keypress.enter="editItem"
          v-model="newName"
          type="text"
          class="form-control"
          id="floatingInput"
          placeholder="Название блокнота"
        />
      </div>
      <div>
        <i
          @click="showEditInput = !showEditInput"
          class="fa-solid fa-pencil"
          style="color: green"
        ></i>
        <i
          class="fa-solid fa-trash-can"
          style="color: red"
          data-bs-toggle="modal"
          data-bs-target="#exampleModal"
        ></i>
      </div>
    </div>

    <!-- Modal -->
    <div
      class="modal fade"
      id="exampleModal"
      tabindex="-1"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog modal-dialog-centered">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">
              Удалить выбранное дело?
            </h5>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">Вы уверены, что хотите его удалить?</div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-secondary"
              data-bs-dismiss="modal"
            >
              Нет
            </button>
            <button
              @click="delItem"
              data-bs-dismiss="modal"
              type="button"
              class="btn btn-primary"
            >
              Да
            </button>
          </div>
        </div>
      </div>
    </div>
  </li>
</template>
<script>
export default {
  name: "note-item",
  props: {
    item: Object,
  },
  data() {
    return {
      showEditInput: false,
      newName: this.item.name,
    };
  },
  methods: {
    changeStatus() {
      this.$emit("changeStatus", this.item);
    },
    delItem() {
      this.$emit("delItem", this.item);
    },
    editItem() {
      if (this.newName) {
        this.$emit("editItem", { item: this.item, newName: this.newName });
        this.showEditInput = false;
      }
    },
  },
};
</script>
<style lang="css">
i {
  padding: 0.5vh;
}
</style>
