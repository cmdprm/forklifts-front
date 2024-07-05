<template>
  <p class="text-2xl font-bold">Справочник погрузчиков</p>
  <div class="flex items-center mt-5">
    <p class="me-5">Номер погрузчика</p>
    <input type="text" class="input-search me-5" required />
    <button class="small-button me-5">Искать</button>
    <button class="reset-button">Сбросить фильтр</button>
  </div>

  <div class="flex items-center mt-10">
    <button class="default-button me-5">Добавить</button>
    <button class="default-button me-5">Изменить</button>
    <button class="default-button me-5">Удалить</button>
    <button class="default-button me-5">Сохранить</button>
    <button class="default-button">Отменить</button>
  </div>

  <div class="mt-10">
    <div class="grid grid-cols-2">
      <div>
        <table
          class="table-auto border-collapse border border-slate-400 bg-white text-center"
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
            <tr>
              <td class="border border-slate-300 p-5">1</td>
              <td class="border border-slate-300 p-5">Амкадор</td>
              <td class="border border-slate-300 p-5">45-65 PH-1</td>
              <td class="border border-slate-300 p-5">500</td>
              <td class="border border-slate-300 p-5">16.04.2024 14:12</td>
            </tr>
          </tbody>
        </table>
      </div>
      <div>
        <div class="mb-5">
          <p class="text-lg font-bold">Простои по погрузчику</p>
          <div class="flex items-center justify-between mt-2">
            <div class="flex">
              <button class="small-button me-5">Добавить</button>
              <button class="small-button me-5">Изменить</button>
              <button class="small-button">Удалить</button>
            </div>
            <div class="flex">
              <button class="small-button me-5">Сохранить</button>
              <button class="small-button">Отменить</button>
            </div>
          </div>
        </div>
        <table
          class="table-auto border-collapse border border-slate-400 bg-white text-center"
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
            <tr>
              <td class="border border-slate-300 p-5">1</td>
              <td class="border border-slate-300 p-5">16.04.2024 14:12</td>
              <td class="border border-slate-300 p-5">16.04.2024 15:47</td>
              <td class="border border-slate-300 p-5">1 ч 35 мин</td>
              <td class="border border-slate-300 p-5">Сломался</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "HomeView",
  components: {},
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
</style>
