<template>
  <div>
    <p class="text-2xl font-bold">Справочник погрузчиков</p>
    <div class="flex items-center mt-5">
      <p class="me-5">Номер погрузчика</p>
      <input
        v-model="searchQuery"
        @input="fetchForklifts"
        type="text"
        class="input-search me-5"
        required
      />
      <button @click="fetchForklifts" class="small-button me-5">Искать</button>
      <button class="reset-button" @click="resetFilter">Сбросить фильтр</button>
    </div>

    <div class="flex items-center mt-10">
      <button
        class="default-button me-5"
        @click="startAddingForklift"
        :disabled="isAddingForklift || isEditingForklift"
      >
        Добавить
      </button>
      <button
        class="default-button me-5"
        @click="startEditingForklift"
        :disabled="!selectedForklift || isAddingForklift"
      >
        Изменить
      </button>
      <button
        class="default-button me-5"
        @click="deleteForklift"
        :disabled="!selectedForklift || isAddingForklift"
      >
        Удалить
      </button>
      <button
        class="default-button me-5"
        @click="saveForkliftChanges"
        v-if="isEditingForklift || isAddingForklift"
      >
        Сохранить
      </button>
      <button
        class="default-button"
        @click="cancelForkliftEditing"
        v-if="isEditingForklift || isAddingForklift"
      >
        Отменить
      </button>
    </div>

    <div class="mt-10">
      <div class="grid grid-cols-2 gap-5">
        <div>
          <table
            class="table-auto border-collapse border border-slate-400 bg-white text-center forklift-table"
          >
            <thead>
              <tr>
                <th class="border border-slate-300 p-5">Код записи</th>
                <th class="border border-slate-300 p-5">Марка</th>
                <th class="border border-slate-300 p-5">Номер</th>
                <th class="border border-slate-300 p-5">Грузоподъемность</th>
                <th class="border border-slate-300 p-5">
                  Время и дата изменения
                </th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-for="forklift in forklifts"
                :key="forklift.id || 'new'"
                @click="!isAddingForklift && selectForklift(forklift)"
                :class="{
                  selected:
                    selectedForklift && selectedForklift.id === forklift.id,
                }"
              >
                <td class="border border-slate-300 p-5">
                  {{ forklift.id || "Новый" }}
                </td>
                <td class="border border-slate-300 p-5">
                  <input
                    v-if="
                      (isEditingForklift &&
                        selectedForklift &&
                        selectedForklift.id === forklift.id) ||
                      (isAddingForklift && newForklift === forklift)
                    "
                    v-model="selectedForklift.brand"
                    class="edit-input"
                    @click.stop
                  />
                  <span v-else>{{ forklift.brand }}</span>
                </td>
                <td class="border border-slate-300 p-5">
                  <input
                    v-if="
                      (isEditingForklift &&
                        selectedForklift &&
                        selectedForklift.id === forklift.id) ||
                      (isAddingForklift && newForklift === forklift)
                    "
                    v-model="selectedForklift.number"
                    class="edit-input"
                    @click.stop
                  />
                  <span v-else>{{ forklift.number }}</span>
                </td>
                <td class="border border-slate-300 p-5">
                  <input
                    v-if="
                      (isEditingForklift &&
                        selectedForklift &&
                        selectedForklift.id === forklift.id) ||
                      (isAddingForklift && newForklift === forklift)
                    "
                    v-model="selectedForklift.capacity"
                    class="edit-input"
                    @click.stop
                  />
                  <span v-else>{{ forklift.capacity }}</span>
                </td>
                <td class="border border-slate-300 p-5">
                  {{
                    forklift.updated_at ? formatDate(forklift.updated_at) : "—"
                  }}
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <div v-if="selectedForklift && !isAddingForklift">
          <div class="mb-5">
            <p class="text-lg font-bold">
              Простои по погрузчику {{ selectedForklift.number }}
            </p>
            <div class="flex items-center justify-between mt-2">
              <div class="flex">
                <button class="small-button me-5" @click="addMalfunction">
                  Добавить
                </button>
                <button
                  class="small-button me-5"
                  @click="editMalfunction"
                  :disabled="!selectedMalfunction"
                >
                  Изменить
                </button>
                <button
                  class="small-button"
                  @click="deleteMalfunction"
                  :disabled="!selectedMalfunction"
                >
                  Удалить
                </button>
              </div>
              <div class="flex">
                <button
                  class="small-button me-5"
                  @click="saveMalfunctionChanges"
                  v-if="isEditingMalfunction"
                >
                  Сохранить
                </button>
                <button
                  class="small-button"
                  @click="cancelMalfunctionEditing"
                  v-if="isEditingMalfunction"
                >
                  Отменить
                </button>
              </div>
            </div>
          </div>
          <table
            class="table-auto border-collapse border border-slate-400 bg-white text-center malfunction-table"
          >
            <thead>
              <tr>
                <th class="border border-slate-300 p-5">Код записи</th>
                <th class="border border-slate-300 p-5">Начало</th>
                <th class="border border-slate-300 p-5">Окончание</th>
                <th class="border border-slate-300 p-5">Время простоя</th>
                <th class="border border-slate-300 p-5">Причина</th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-for="malfunction in malfunctions"
                :key="malfunction.id"
                @click="selectMalfunction(malfunction)"
                :class="{
                  selected:
                    selectedMalfunction &&
                    selectedMalfunction.id === malfunction.id,
                }"
              >
                <td class="border border-slate-300 p-5">
                  {{ malfunction.id }}
                </td>
                <td class="border border-slate-300 p-5">
                  <input
                    v-if="
                      isEditingMalfunction &&
                      selectedMalfunction &&
                      selectedMalfunction.id === malfunction.id
                    "
                    v-model="selectedMalfunction.detected_at"
                    class="edit-input"
                  />
                  <span v-else>{{ formatDate(malfunction.detected_at) }}</span>
                </td>
                <td class="border border-slate-300 p-5">
                  <input
                    v-if="
                      isEditingMalfunction &&
                      selectedMalfunction &&
                      selectedMalfunction.id === malfunction.id
                    "
                    v-model="selectedMalfunction.resolved_at"
                    class="edit-input"
                  />
                  <span v-else>{{ formatDate(malfunction.resolved_at) }}</span>
                </td>
                <td class="border border-slate-300 p-5">
                  {{ calculateDowntime(malfunction) }}
                </td>
                <td class="border border-slate-300 p-5">
                  <input
                    v-if="
                      isEditingMalfunction &&
                      selectedMalfunction &&
                      selectedMalfunction.id === malfunction.id
                    "
                    v-model="selectedMalfunction.description"
                    class="edit-input"
                  />
                  <span v-else>{{ malfunction.description }}</span>
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <div v-else>
          <p class="text-lg font-bold">
            Выберите погрузчик, чтобы увидеть его простои
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import apiClient from "@/services/axios.js";

export default {
  name: "HomeView",
  data() {
    return {
      forklifts: [],
      malfunctions: [],
      searchQuery: "",
      selectedForklift: null,
      isEditingForklift: false,
      originalForklift: null,
      newForklift: null,
      isAddingForklift: false,
      selectedMalfunction: null,
      isEditingMalfunction: false,
      originalMalfunction: null,
    };
  },
  methods: {
    async fetchForklifts() {
      try {
        const response = await apiClient.get("search-forklifts", {
          params: {
            number: this.searchQuery,
          },
        });
        this.forklifts = response.data;
      } catch (error) {
        console.error("Ошибка при получении данных о погрузчиках:", error);
      }
    },
    async fetchMalfunctions() {
      if (!this.selectedForklift) return;

      try {
        const response = await apiClient.get(
          `forklifts/${this.selectedForklift.id}/malfunctions`
        );
        this.malfunctions = response.data;
      } catch (error) {
        console.error("Ошибка при получении данных по простоям:", error);
      }
    },
    formatDate(isoDate) {
      if (!isoDate) return "";

      const date = new Date(isoDate);

      const day = date.getDate().toString().padStart(2, "0");
      const month = (date.getMonth() + 1).toString().padStart(2, "0");
      const year = date.getFullYear();

      const hours = date.getHours().toString().padStart(2, "0");
      const minutes = date.getMinutes().toString().padStart(2, "0");

      return `${day}.${month}.${year} ${hours}:${minutes}`;
    },
    selectForklift(forklift) {
      this.selectedForklift = { ...forklift };
      this.originalForklift = { ...forklift };
      this.isEditingForklift = false;
      this.isAddingForklift = false;
      this.newForklift = null;

      console.log("Выбрано:", this.selectedForklift);

      this.fetchMalfunctions();
    },
    startAddingForklift() {
      this.newForklift = {
        id: null,
        brand: "",
        number: "",
        capacity: "",
        updated_at: null,
      };
      this.forklifts.push(this.newForklift);
      this.isAddingForklift = true;
      this.selectedForklift = null;
    },
    startEditingForklift() {
      if (this.selectedForklift) {
        this.isEditingForklift = true;
      }
    },
    async saveForkliftChanges() {
      if (this.isAddingForklift) {
        try {
          const response = await apiClient.post("forklifts", this.newForklift);
          this.newForklift.id = response.data.id;
          this.newForklift.updated_at = response.data.updated_at;
          this.isAddingForklift = false;
          this.newForklift = null;

          // Обновить список погрузчиков после добавления нового
          this.fetchForklifts();
        } catch (error) {
          console.error("Ошибка при добавлении погрузчика:", error);
          // Удалить новый погрузчик из списка, если сохранение не удалось
          this.forklifts.pop();
          this.newForklift = null;
          this.isAddingForklift = false;
        }
      } else if (this.isEditingForklift) {
        const response = await apiClient.put(
          `forklifts/${this.selectedForklift.id}`,
          this.selectedForklift
        );

        console.log(response);

        this.isEditingForklift = false;

        this.fetchForklifts();
      }
    },
    cancelForkliftEditing() {
      if (this.isAddingForklift) {
        // Удалить новый погрузчик из списка при отмене добавления
        this.forklifts.pop();
        this.isAddingForklift = false;
        this.newForklift = null;
      } else if (this.isEditingForklift) {
        // Восстановить изначальное состояние выбранного погрузчика при отмене редактирования
        this.selectedForklift = { ...this.originalForklift };
        this.isEditingForklift = false;
      }
    },
    async deleteForklift() {
      if (this.selectedForklift && !this.isAddingForklift) {
        try {
          await apiClient.delete(`forklifts/${this.selectedForklift.id}`);
          this.forklifts = this.forklifts.filter(
            (forklift) => forklift.id !== this.selectedForklift.id
          );
          this.selectedForklift = null;
          this.malfunctions = [];
        } catch (error) {
          console.error("Ошибка при удалении погрузчика:", error);
        }
      }
    },
    selectMalfunction(malfunction) {
      this.selectedMalfunction = { ...malfunction };
      this.originalMalfunction = { ...malfunction };
    },
    addMalfunction() {
      // Логика добавления нового простоя
    },
    editMalfunction() {
      this.isEditingMalfunction = true;
    },
    saveMalfunctionChanges() {
      // Логика сохранения изменений простоя
      this.isEditingMalfunction = false;
    },
    cancelMalfunctionEditing() {
      this.selectedMalfunction = { ...this.originalMalfunction };
      this.isEditingMalfunction = false;
    },
    deleteMalfunction() {
      // Логика удаления простоя
    },
    calculateDowntime(malfunction) {
      if (!malfunction.detected_at || !malfunction.resolved_at) return "";

      const detected = new Date(malfunction.detected_at);
      const resolved = new Date(malfunction.resolved_at);
      const downtime = Math.abs(resolved - detected) / 1000; // Время простоя в секундах

      const hours = Math.floor(downtime / 3600);
      const minutes = Math.floor((downtime % 3600) / 60);

      return `${hours} ч ${minutes} мин`;
    },
    resetFilter() {
      this.searchQuery = "";
      this.fetchForklifts();
    },
  },
  mounted() {
    this.fetchForklifts();
  },
};
</script>

<style>
.input-search {
  padding: 8px 16px;
  border-radius: 5em;
}

.input-search:focus {
  outline: none;
}

.small-button {
  padding: 8px 20px;
  background: crimson;
  color: white;
  border-radius: 0.75em;
}

.small-button:active {
  background: rgb(176, 16, 48);
}

.reset-button {
  border: none;
  text-decoration: underline;
}

.default-button {
  padding: 8px;
  width: 14em;
  background: crimson;
  color: white;
  border-radius: 0.75em;
}

.default-button:active {
  background: rgb(176, 16, 48);
}

.forklift-table,
.malfunction-table {
  width: 100%;
  border-collapse: collapse;
}

.forklift-table th,
.forklift-table td,
.malfunction-table th,
.malfunction-table td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

.forklift-table th,
.malfunction-table th {
  background-color: #f2f2f2;
}

.forklift-table tr:hover,
.malfunction-table tr:hover {
  background-color: #f5f5f5;
  cursor: pointer;
}

.forklift-table .selected,
.malfunction-table .selected {
  background-color: #b3d9ff;
}

.edit-input {
  width: 100%;
  padding: 8px;
  box-sizing: border-box;
}
</style>
