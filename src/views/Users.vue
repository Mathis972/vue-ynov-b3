<template>
  <div class="container-fluid">
    <h2
      v-if="$route.query.edited"
      style="background: green; color: white"
    >
      Successful edit
    </h2>
    <div id="app">
      <div>
        <h1>Vue</h1>
        <hr>
        <navbar
          v-model="gender"
          :filtered-list-count="filteredLength"
          :non-filtered-users="nonFilteredUsers.length"
          @get-users="fetchUsers"
          @filter-search="searchString = $event"
          @notification="$emit('notification', $event)"
        />
      </div>
      <user-list
        :gender="gender"
        :non-filtered-users="nonFilteredUsers"
        :search-string="searchString"
        :loading="loading"
        @update-count="filteredLength = $event"
        @delete-user="deleteUser($event)"
      />
    </div>
  </div>
</template>
<script>
import Navbar from '@/components/Navbar.vue';
import axios from 'axios';
import userList from '../components/UserList.vue';

export default {
  name: 'Users',
  components: {
    Navbar,
    userList,
  },
  data() {
    return {
      gender: '',
      nonFilteredUsers: [],
      searchString: '',
      filteredLength: 0,
      loading: true,
    };
  },
  created() {
    this.fetchUsers();
  },
  methods: {
    fetchUsers() {
      this.loading = true;
      axios('https://ynov-front.herokuapp.com/api/users').then(({ data }) => {
        this.nonFilteredUsers = data.data.map((user) => ({
          id: user._id,
          age: this.getAge(user.birthDate),
          birthDate: user.birthDate,
          name: `${user.firstName} ${user.lastName.toUpperCase()}`,
          firstName: user.firstName,
          lastName: user.lastName,
          email: user.email,
          phone: user.phone,
          gender: user.gender,
          avatar: user.avatarUrl,
        }));
        this.loading = false;
      });
    },
    getAge(dateString) {
      const today = new Date();
      const birthDate = new Date(dateString);
      let age = today.getFullYear() - birthDate.getFullYear();
      const m = today.getMonth() - birthDate.getMonth();
      if (m < 0 || (m === 0 && today.getDate() < birthDate.getDate())) {
        age -= 1;
      }
      return age;
    },
    deleteUser(id) {
      console.log(id);
      this.$emit('notification', { type: 'error', message: `Deleted user ${id}` });
      this.fetchUsers();
      // axios.delete('https://ynov-front.herokuapp.com/api/users', id)
      //   .then(() => {
      //     this.$emit('notification', { type: 'error', message: 'del' });
      //     this.fetchUsers();
      //   });
    },
  },
};
</script>
<style>

  </style>
