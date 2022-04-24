<script setup>
import { ref, computed } from 'vue'

// データを定義
const namesOfCats = ref([
  {nameEn:'Leo', nameJa:'レオ', age:10},
  {nameEn:'Bella',nameJa:'ベル', age:13},
  {nameEn:'Milo', nameJa:'マイロ',age:4},
  {nameEn:'Charlie',nameJa:'チャーリー', age:7},
  {nameEn:'Kitty', nameJa:'キティ', age:3},
  {nameEn:'Kitty2', nameJa:'キティ2', age:3},
]);
const namesOfDogs = ref([
  {nameEn:'Abby', nameJa:'アビー', age:10},
  {nameEn:'Carly',nameJa:'カーリー', age:13},
  {nameEn:'Luna', nameJa:'ルナ',age:4},
  {nameEn:'Mocha',nameJa:'モカ', age:7},
  {nameEn:'Rose', nameJa:'ローズ', age:3},
]);

// 追加用
const pushInputNameEn = ref("");
const pushInputNameJa = ref("");
const pushInputAge = ref("");
const pushAnimal = (array) => { if(pushInputNameEn.value) {
  array.push({
    nameEn:pushInputNameEn.value, 
    nameJa:pushInputNameJa.value, 
    age:pushInputAge.value
    });
  pushInputNameEn.value = "";
  pushInputNameJa.value = "";
  pushInputAge.value = "";
}};

// 削除用
const deletCat = (cat) => {namesOfCats.value.splice(namesOfCats.value.indexOf(cat), 1)};
const deletDog = (dog) => {namesOfDogs.value.splice(namesOfDogs.value.indexOf(dog), 1)};

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
</script>

<template>

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
<h3><a href="#catList">猫該当:{{filteredCats.length}}</a> <a href="#dogList">犬該当:{{filteredDogs.length}}</a></h3>
</div>

<!-- 猫のリスト -->
<div v-if="filteredCats.length" id="catList">
  <h3>ねこ</h3>
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
  <h3>いぬ</h3>
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

</template>

<style scoped>
a {
  margin: 10px;
  text-decoration: none;
}
table {
  margin: auto;
  margin-top: 10px;
  padding: 10px;
  border-collapse: collapse;
  width: 60%;
}
td {
  text-align: left;
  padding: 10px;
  border: 1px solid #dddddd;
}
input {
  margin: 5px;
}
.noResult {
  text-decoration: underline;
}
.searchInput {
  width: 30%;
}
button {
  margin: 5px;
}
</style>