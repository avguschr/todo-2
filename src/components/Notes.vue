<template>
  <div>
    <div class="d-flex justify-content-between align-items-center mb-3">
      <div v-if="showSearchInput" class="form-floating text-start w-100">
        <input
          @keypress.enter="createNote"
          v-model="searchString"
          type="text"
          class="form-control"
          id="floatingInput"
          placeholder="Введите строку для поиска"
        />
        <label for="floatingInput">Поиск</label>
      </div>

      <div v-else class="form-floating text-start w-100">
        <input
          @keypress.enter="createNote"
          v-model="noteName"
          type="text"
          class="form-control"
          id="floatingInput"
          placeholder="Название блокнота"
        />
        <label for="floatingInput">Название блокнота</label>
      </div>
      <div class="p-2 d-flex align-items-center justify-content-between">
        <i @click="createNote" class="fa-solid fa-plus display-6"></i>
        <i
          @click="showSearchInput = true"
          class="fa-solid fa-magnifying-glass display-6"
        ></i>
      </div>
    </div>
    <div v-if="showSearchInput">
      <div v-if="searchNotes.length">
        <div v-for="note in searchNotes" class="notes" :key="note.id">
          <note
            @changeColor="changeColor"
            @editNote="editNote"
            @delNote="delNote"
            @editNoteItem="editNoteItem"
            @addItem="addNoteItem"
            @changeItemStatus="changeItemStatus"
            @delNoteItem="delNoteItem"
            :note="note"
          />
        </div>
      </div>
      <div class="text-start" v-else>Ничего не найдено</div>
    </div>
    <div v-else>
      <div class="mb-3">
        <select
          @change="sortNotes"
          v-model="selectedSort"
          class="form-select"
          aria-label="Default select example"
        >
          <option value="0" selected>Нет сортировки</option>
          <option value="1">Сортировка по названию</option>
          <option value="2">Сортировка по дате</option>
        </select>
      </div>

      <div v-if="selectedSort === '1'">
        <div v-for="note in sortName" class="notes" :key="note.id">
          <note
            @changeColor="changeColor"
            @editNote="editNote"
            @delNote="delNote"
            @editNoteItem="editNoteItem"
            @addItem="addNoteItem"
            @changeItemStatus="changeItemStatus"
            @delNoteItem="delNoteItem"
            :note="note"
          />
        </div>
      </div>

      <div v-else-if="selectedSort === '2'">
        <div v-for="note in sortDate" class="notes" :key="note.id">
          <note
            @changeColor="changeColor"
            @editNote="editNote"
            @delNote="delNote"
            @editNoteItem="editNoteItem"
            @addItem="addNoteItem"
            @changeItemStatus="changeItemStatus"
            @delNoteItem="delNoteItem"
            :note="note"
          />
        </div>
      </div>

      <draggable
        v-else
        style="padding-bottom: 7vh"
        @end="end"
        :list="notes"
        group="notes"
      >
        <transition-group>
          <div v-for="note in notes" class="notes" :key="note.id">
            <note
              @changeColor="changeColor"
              @editNote="editNote"
              @delNote="delNote"
              @editNoteItem="editNoteItem"
              @addItem="addNoteItem"
              @changeItemStatus="changeItemStatus"
              @delNoteItem="delNoteItem"
              :note="note"
            />
          </div>
        </transition-group>
      </draggable>
    </div>
  </div>
</template>
<script>
import Note from "./Note.vue";
import { format } from "date-fns";
import ru from "date-fns/locale/ru";
import { v4 as uuidv4 } from "uuid";
import { VueDraggableNext } from "vue-draggable-next";
export default {
  components: { Note, draggable: VueDraggableNext },
  name: "notes-component",
  data() {
    return {
      noteName: "",
      notes: [],
      showSearchInput: false,
      searchString: "",
      selectedSort: 0,
    };
  },
  computed: {
    searchNotes() {
      return this.notes.filter(
        (note) => note.name.indexOf(this.searchString) !== -1
      );
    },
    sortName() {
      return this.sortNotesName();
    },
    sortDate() {
      return this.sortNotesDate();
    },
  },
  methods: {
    sortNotesName() {
      const sorted = [...this.notes];
      return sorted.sort((a, b) => {
        if (a.name > b.name) {
          return 1;
        }
        if (a.name < b.name) {
          return -1;
        }
        return 0;
      });
    },
    sortNotesDate() {
      const sorted = [...this.notes];
      return sorted.sort((a, b) => {
        if (a.rawDate > b.rawDate) {
          return 1;
        }
        if (a.rawDate < b.rawDate) {
          return -1;
        }
        return 0;
      });
    },
    sortNotes() {
      console.log(this.selectedSort);
    },
    end() {
      localStorage.notes = JSON.stringify(this.notes);
    },
    changeColor(obj) {
      const noteIndex = this.notes.findIndex((note) => note.id === obj.note.id);
      this.notes[noteIndex].color = obj.newColor;
      localStorage.notes = JSON.stringify(this.notes);
    },
    createNote() {
      if (this.showSearchInput) {
        this.showSearchInput = false;
        this.searchString = "";
      } else {
        if (this.noteName) {
          this.notes.unshift({
            id: uuidv4(),
            name: this.noteName,
            color: "#ededed",
            date: format(new Date(), "d MMM y г. в H:mm", { locale: ru }),
            rawDate: new Date(),
            items: [],
          });
          this.noteName = "";
          localStorage.notes = JSON.stringify(this.notes);
        }
      }
    },
    addNoteItem(newItem) {
      const noteIndex = this.notes.findIndex(
        (note) => note.id === newItem.parentId
      );
      this.notes[noteIndex].items.push(newItem);
      localStorage.notes = JSON.stringify(this.notes);
    },
    changeItemStatus(item) {
      const noteIndex = this.notes.findIndex(
        (note) => note.id === item.parentId
      );
      const itemIndex = this.notes[noteIndex].items.findIndex(
        (i) => item.id === i.id
      );
      this.notes[noteIndex].items[itemIndex].done =
        !this.notes[noteIndex].items[itemIndex].done;

      localStorage.notes = JSON.stringify(this.notes);
    },
    delNoteItem(item) {
      const noteIndex = this.notes.findIndex(
        (note) => note.id === item.parentId
      );
      const itemIndex = this.notes[noteIndex].items.findIndex(
        (i) => item.id === i.id
      );
      this.notes[noteIndex].items.splice(itemIndex, 1);
      localStorage.notes = JSON.stringify(this.notes);
    },
    editNoteItem(obj) {
      const noteIndex = this.notes.findIndex(
        (note) => note.id === obj.item.parentId
      );
      const itemIndex = this.notes[noteIndex].items.findIndex(
        (i) => obj.item.id === i.id
      );
      this.notes[noteIndex].items[itemIndex].name = obj.newName;
      localStorage.notes = JSON.stringify(this.notes);
    },
    delNote(note) {
      const noteIndex = this.notes.findIndex((n) => n.id === note.id);
      this.notes.splice(noteIndex, 1);
      localStorage.notes = JSON.stringify(this.notes);
    },
    editNote(obj) {
      const noteIndex = this.notes.findIndex((note) => note.id === obj.note.id);
      this.notes[noteIndex].name = obj.newName;
      localStorage.notes = JSON.stringify(this.notes);
    },
  },
  mounted() {
    if (localStorage.notes) {
      this.notes = JSON.parse(localStorage.notes);
    } else {
      localStorage.setItem("notes", JSON.stringify([]));
    }
  },
};
</script>
<style lang="scss">
i {
  cursor: pointer;
}
</style>
