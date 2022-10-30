<script setup lang="ts">
const { useApi } = useAuth();
const { data: records } = await useApi("/api/researchs");
let original = [];
original = [...records.value];

const gender = ref("");
const vaccines = ref("");
const postal = ref("");
const dob = ref("");
const totalInfection = ref("");

function filterData() {
  let filteredResults = [];
  filteredResults = original;
  if (gender.value !== "") {
    filteredResults = filteredResults.filter((s) => s.gender === gender.value);
  }
  if (vaccines.value !== "") {
    filteredResults = filteredResults.filter((s) =>
      s.list_of_vaccines.includes(vaccines.value)
    );
  }
  if (postal.value !== "") {
    filteredResults = filteredResults.filter((s) =>
      s.postal_code.includes(postal.value)
    );
  }
  if (dob.value !== "") {
    filteredResults = filteredResults.filter((s) => {
      if (s.dob === dob.value) {
        return true;
      }
      if (s.dob.includes("-")) {
        const result = s.dob.split("-");
        if (result.length === 2) {
          return between(dob.value, result[0], result[1]);
        }
      }
      return false;
    });
  }
  if (totalInfection.value !== "") {
    filteredResults = filteredResults.filter(
      (s) => s.total_infection === totalInfection.value
    );
  }
  records.value = filteredResults;
}
function between(x, min, max) {
  x = parseInt(x);
  min = parseInt(min);
  max = parseInt(max);
  if (isNaN(x) || isNaN(min) || isNaN(max)) {
    return false;
  }
  if (min > max) {
    return false;
  }
  return x >= min && x <= max;
}
</script>
<template>
  <div class="container flex w-full mx-auto my-4">
    <div class="overflow-x-auto w-full relative shadow-md sm:rounded-lg">
      <table class="w-full text-sm text-left text-gray-500 dark:text-gray-400">
        <caption
          class="p-5 text-lg font-semibold text-left text-gray-900 bg-white dark:text-white dark:bg-gray-800"
        >
          Researcher DB
          <div class="py-4">
            <div class="flex gap-2 flex-wrap">
              <input
                v-model="vaccines"
                type="text"
                class="block p-2 w-80 text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
                placeholder="Search for vaccines"
              />
              <input
                v-model="postal"
                type="text"
                class="block p-2 w-80 text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
                placeholder="Search for postal"
              />
              <input
                v-model="dob"
                type="text"
                class="block p-2 w-80 text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
                placeholder="Search for dob"
              />
              <input
                v-model="totalInfection"
                type="number"
                class="block p-2 w-80 text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
                placeholder="Search for total infection"
              />
              <select
                id="filterGender"
                v-model="gender"
                class="block p-2 w-30 text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
              >
                <option disabled value="">Please select gender</option>
                <option value="">any</option>
                <option value="Male">Male</option>
                <option value="Female">Female</option>
              </select>
            </div>
            <div class="mt-4">
              <button
                class="block p-2 w-80 text-sm text-gray-900 bg-gray-50 rounded-lg border border-gray-300 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
                @click="filterData"
              >
                Search
              </button>
            </div>
          </div>
        </caption>
        <thead
          class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400"
        >
          <tr>
            <th scope="col" class="py-3 px-6">Date of Birth</th>
            <th scope="col" class="py-3 px-6">Gender</th>
            <th scope="col" class="py-3 px-6">Postal Code</th>
            <th scope="col" class="py-3 px-6">List of vaccines</th>
            <th scope="col" class="py-3 px-6">Latest close contact</th>
            <th scope="col" class="py-3 px-6">Latest infected date</th>
            <th scope="col" class="py-3 px-6">Total infection</th>
            <th scope="col" class="py-3 px-6">
              Total close contact as infected
            </th>
            <th scope="col" class="py-3 px-6">
              Total close contact with infected
            </th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(record, index) in records"
            :key="index"
            class="bg-white border-b dark:bg-gray-800 dark:border-gray-700"
          >
            <td class="py-4 px-6">{{ record.dob }}</td>
            <td class="py-4 px-6">{{ record.gender }}</td>
            <td class="py-4 px-6">{{ record.postal_code }}</td>
            <td class="py-4 px-6">{{ record.list_of_vaccines }}</td>
            <td class="py-4 px-6">{{ record.last_close_contact }}</td>
            <td class="py-4 px-6">{{ record.last_infected_date }}</td>
            <td class="py-4 px-6">{{ record.total_infection }}</td>
            <td class="py-4 px-6">
              {{ record.total_close_contact_as_infected }}
            </td>
            <td class="py-4 px-6">
              {{ record.total_close_contact_with_infected }}
            </td>
          </tr>
        </tbody>
      </table>
      <div
        v-if="records && (records as Array<any>).length === 0"
        class="text-center p-4 dark:bg-gray-800"
      >
        No records found
      </div>
    </div>
  </div>
</template>
