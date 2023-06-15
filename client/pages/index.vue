<template>
	<div class="mx-auto max-w-5xl py-10">
		<h3 class="text-2xl">Users</h3>
		<div>
			<FormButton type="button" buttonStyle="primary" @click="navigateTo('/users/new')">NEW USER</FormButton>
		</div>
		<div>
			<div v-if="pending">
				Loading ...
			</div>
			<div v-else>
				<table class="min-w-full text-left text-sm font-light">
					<thead class="border-b font-medium dark:border-neutral-500">
						<tr>
							<th scope="col" class="px-6 py-2">Name</th>
							<th scope="col" class="px-6 py-2">Course</th>
							<th scope="col" class="px-6 py-2">Is Active</th>
							<th scope="col" class="px-6 py-2">Action</th>
						</tr>
					</thead>
					<tbody>
						<tr class="border-b dark:border-neutral-500" v-for="user in users">
							<td class="whitespace-nowrap px-6 py-2 font-medium">{{ user.name }}</td>
							<td class="whitespace-nowrap px-6 py-2">{{ user.course }}</td>
							<td class="whitespace-nowrap px-6 py-2">
								<span v-if="user.is_active" class="text-green-700">✓</span>
								<span v-else class="text-red-700">X</span>
							</td>
							<td class="whitespace-nowrap px-6 py-2">
								<a class="cursor-pointer hover:underline bg-red-500 mx-1 px-3 py-1 text-white" @click="viewUser(user.id)">
									VIEW
								</a>
								<a class="cursor-pointer hover:underline bg-blue-500 mx-1 px-3 py-1 text-white" @click="editUser(user.id)">
									EDIT
								</a>
							</td>
						</tr>
					</tbody>
				</table>
			<div>
			<FormButtonPage type="button" buttonStyle="primary" @click="previousPage" :disabled="currentPage === 1">PREVIOUS
			</FormButtonPage>
			<FormButtonPage type="button" buttonStyle="primary" @click="nextPage" :disabled="currentPage === totalPages">NEXT
			</FormButtonPage>
			<div>
        </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

const currentPage = ref(1);
const perPage = 10;
const totalPages = ref(0);
const users = ref([]);

onMounted(() => {
  fetchData();
});

function viewUser(userId) {
  navigateTo("/users/" + userId);
}

function editUser(userId) {
  navigateTo("/users/edit?user_id=" + userId);
}

async function previousPage() {
  if (currentPage.value > 1) {
    currentPage.value--;
    await fetchData();
  }
}

async function nextPage() {
  if (currentPage.value < totalPages.value) {
    currentPage.value++;
    await fetchData();
  }
}

async function fetchData() {
  const response = await fetch(
    `http://127.0.0.1:8000/api/users?page=${currentPage.value}&perPage=${perPage}`
  );
  const responseData = await response.json();
  users.value = responseData.data;
  totalPages.value = Math.ceil(responseData.total / perPage);
}
</script>