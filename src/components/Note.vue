<template>
  <div class="card text-start mb-4">
    <h5
      :style="{ background: note.color }"
      v-if="!showEditInput"
      @click="showInputCreate = !showInputCreate"
      class="card-header"
    >
      {{ note.name }}
      <i @click="createNote" class="fa-solid fa-plus"></i>
    </h5>
    <input
      v-else
      @keypress.enter="editNote"
      v-model="newNoteName"
      type="text"
      class="form-control m-2 w-auto"
      id="floatingInput"
      placeholder="Название блокнота"
    />
    <div class="card-body">
      <div v-if="showInputCreate" class="form-floating text-start w-100 mb-2">
        <input
          @keypress.enter="createItem"
          v-model="itemName"
          type="text"
          class="form-control"
          id="floatingInput"
          placeholder="Название блокнота"
        />
        <label for="floatingInput">Напишите дело</label>
      </div>
      <div v-if="note.items.length" class="card mb-2">
        <ul class="list-group list-group-flush">
          <item
            @editItem="editNoteItem"
            @delItem="delNoteItem"
            @changeStatus="changeItemStatus"
            :item="item"
            v-for="item in note.items"
            :key="item.id"
          />
        </ul>
      </div>
      <div v-else class="mb-2">Дел пока нет</div>
      <div class="d-flex justify-content-between align-items-center">
        <div>{{ note.date }}</div>
        <div class="d-flex">
          <i
            @click="showEditInput = !showEditInput"
            class="fa-solid fa-pencil"
            style="color: green"
          ></i>
          <i
            data-bs-toggle="modal"
            data-bs-target="#exampleModal"
            class="fa-solid fa-trash-can"
            style="color: red"
          ></i>
          <input
            @change="changeColor"
            id="color"
            name="color"
            type="color"
            v-model="color"
          />
          <label for="color"><i class="color fa-solid fa-palette"></i></label>
        </div>
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
              Удалить выбранный блокнот?
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
              @click="delNote"
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
  </div>
</template>
<script>
import Item from "./Item.vue";
import { v4 as uuidv4 } from "uuid";
export default {
  name: "note-component",
  components: {
    Item,
  },
  props: {
    note: Object,
  },
  data() {
    return {
      showInputCreate: false,
      itemName: "",
      newNoteName: this.note.name,
      showEditInput: false,
      color: "#ededed",
    };
  },
  methods: {
    changeColor() {
      this.$emit("changeColor", { note: this.note, newColor: this.color });
    },
    delNote() {
      this.$emit("delNote", this.note);
    },
    editNote() {
      if (this.newNoteName) {
        this.$emit("editNote", { note: this.note, newName: this.newNoteName });
        this.showEditInput = false;
      }
    },
    createItem() {
      if (this.itemName) {
        const newItem = {
          id: uuidv4(),
          parentId: this.note.id,
          name: this.itemName,
          done: false,
        };
        this.$emit("addItem", newItem);
        this.showInputCreate = false;
        this.itemName = "";
      }
    },
    changeItemStatus(item) {
      this.$emit("changeItemStatus", item);
    },
    delNoteItem(item) {
      this.$emit("delNoteItem", item);
    },
    editNoteItem(obj) {
      this.$emit("editNoteItem", obj);
    },
  },
};
</script>
<style lang="css">
h5 {
  cursor: pointer;
}
input[type="color"] {
  all: unset;
  opacity: 0;
}

.color {
  animation: change-color linear 7s infinite;
}

@keyframes change-color {
  0% {
    color: red;
  }
  14% {
    color: orangered;
  }
  28% {
    color: yellow;
  }
  42% {
    color: green;
  }
  56% {
    color: #42aaff;
  }
  70% {
    color: blue;
  }
  84% {
    color: #8b00ff;
  }
  100% {
    color: red;
  }
}
</style>
