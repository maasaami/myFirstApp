<script setup>
import { ref, computed, onMounted } from 'vue'

// データを定義
const namesOfCats = ref([])
const namesOfDogs = ref([])

onMounted(() => {
  fetch("http://localhost:4000/cats")
    .then(res => res.json())
    .then(data => namesOfCats.value = data)
    .catch(err => console.log(err.message));

  fetch("http://localhost:4000/dogs")
    .then(res => res.json())
    .then(data => namesOfDogs.value = data)
    .catch(err => console.log(err.message))
})

// 追加用
const pushInputNameEn = ref("");
const pushInputNameJa = ref("");
const pushInputAge = ref("");
const pushAnimal = (array) => {
  if (pushInputNameEn.value) {
    array.push({
      nameEn: pushInputNameEn.value,
      nameJa: pushInputNameJa.value,
      age: pushInputAge.value
    });
    pushInputNameEn.value = "";
    pushInputNameJa.value = "";
    pushInputAge.value = "";
  }
};

// 削除用
const deletCat = cat => namesOfCats.value.splice(namesOfCats.value.indexOf(cat), 1);
const deletDog = dog => namesOfDogs.value.splice(namesOfDogs.value.indexOf(dog), 1);

// 検索用
const searchInput = ref("");
const exactMatch = ref(false)
function filterAnimal(animals) {
  if (exactMatch.value) {
    return animals.filter(animal => animal.nameEn.toLowerCase() === searchInput.value.toLowerCase() || animal.nameJa === searchInput.value)
  }
  else {
    return animals.filter(animal => animal.nameEn.toLowerCase().indexOf(searchInput.value.toLowerCase()) !== -1 || animal.nameJa.indexOf(searchInput.value) !== -1)
  }
}
const filteredCats = computed(() => filterAnimal(namesOfCats.value));
const filteredDogs = computed(() => filterAnimal(namesOfDogs.value));

// ダウンロード用
function downloadCSV(list) {
  let header = Object.keys(list[0]).join(",") + "\n";

  let body = list.map(function (d) {
    return Object.keys(d).map(function (key) {
      return d[key];
    }).join(',');
  }).join("\n");

  // const csv = header + body

  const anchor = document.createElement('a');
  anchor.href = 'data:text/csv;charset=utf-8,' + encodeURIComponent(header + body);
  anchor.target = '_blank';
  anchor.download = 'Animal List.csv';
  anchor.click();

};
</script>

<template>

  <div class="container">
    <!-- 動物追加 -->
    <div>
      <input v-model="pushInputNameEn" placeholder="名前(英語)">
      <input v-model="pushInputNameJa" placeholder="名前(日本語)">
      <input type="number" v-model="pushInputAge" placeholder="年齢">
      <button @click="pushAnimal(namesOfCats)">ねこ追加！</button>
      <button @click="pushAnimal(namesOfDogs)">いぬ追加！</button>
      <br>
    </div>

    <!-- 検索フォーム -->
    <div>
      <input v-model="searchInput" placeholder="検索" class="searchInput">
      <label><input type="checkbox" v-model="exactMatch">完全一致</label>
      <h3><a href="#catList">猫該当:{{ filteredCats.length }}</a> <a href="#dogList">犬該当:{{ filteredDogs.length }}</a></h3>
    </div>

    <!-- 猫のリスト -->
    <div v-if="filteredCats.length" id="catList">
      <h2>ねこ</h2>
      <div style="text-align: right;">
        <button @click="downloadCSV(filteredCats)">CSV Download ↓</button>
      </div>
      <table>
        <th>名前 (英語)</th>
        <th>名前 (日本語)</th>
        <th>年齢</th>
        <tr v-for="cat in filteredCats">
          <td>{{ cat.nameEn }}</td>
          <td>{{ cat.nameJa }}</td>
          <td>{{ cat.age }}</td>
          <td style="text-align: center;">
            <button @click="deletCat(cat)">削除</button>
            <!-- <button @click="editCat(cat)">編集</button> -->
          </td>
        </tr>
      </table>
    </div>

    <!-- 犬のリスト -->
    <div v-if="filteredDogs.length" id="dogList">
      <h2>いぬ</h2>
      <div style="text-align: right;">
        <button @click="downloadCSV(filteredDogs)">CSV Download ↓</button>
      </div>
      <table>
        <th>名前 (英語)</th>
        <th>名前 (日本語)</th>
        <th>年齢</th>
        <tr v-for="dog in filteredDogs">
          <td>{{ dog.nameEn }}</td>
          <td>{{ dog.nameJa }}</td>
          <td>{{ dog.age }}</td>
          <td style="text-align: center;">
            <button @click="deletDog(dog)">削除</button>
            <!-- <button @click="editCat(cat)">編集</button> -->
          </td>
        </tr>
      </table>
    </div>
  </div>
</template>

<style scoped>
.container {
  width: 80%;
  margin: auto;
}

a {
  margin: 10px;
  text-decoration: none;
}

table {
  margin: auto;
  padding: 10px;
  border-collapse: collapse;
  width: 100%;
}

td {
  text-align: left;
  padding: 10px;
  border: 1px solid #dddddd;
}

input {
  margin: 5px;
}

.searchInput {
  width: 30%;
}

button {
  margin: 5px;
}
</style>