<template>
  <div
    style="display: flex; flex-direction: row; justify-content: space-evenly;
   align-items: center"
  >
    <button
      v-if="!modalVisible"
      class="mg"
      @click="modalVisible = true"
    >
      +
    </button>
    <transition
      name="fade"
    >
      <user-modal
        v-if="modalVisible"
        @close="modalVisible = false"
        @rfrsh-users="$emit('get-users')"
        @notify="$emit('notification', $event)"
      />
    </transition>

    <button
      id="fetch-users"
      class="btn btn-primary mg"
      @click="$emit('get-users')"
    >
      Fetch users
    </button>
    <select
      id="genders"
      name="genders"
      class="mg"
      @change="$emit('filter-genre', $event.target.value)"
    >
      <option value="">
        All
      </option>

      <option value="male">
        Male
      </option>

      <option value="female">
        Female
      </option>
    </select>
    <span
      v-if="gender != ''"
      class="counter"
    > {{ filteredListCount }} {{ gender }}{{ filteredListCount > 1 ? 's' : null }} /
      {{ nonFilteredUsers }} users</span>
    <span
      v-else
      class="counter"
    > {{ filteredListCount }} / {{ nonFilteredUsers }} users</span>
    <input
      placeholder="search"
      style="margin-left: auto;"
      @input="$emit('filter-search', $event.target.value)"
    >
  </div>
</template>

<script>
import UserModal from './UserModal.vue';

export default {
  components: { UserModal },
  model: {
    prop: 'gender',
    event: 'filter-genre',
  },
  props: {
    filteredListCount: { type: Number, default: 0 },
    nonFilteredUsers: { type: Number, default: 0 },
    gender: { type: String, default: '' },
  },
  data() {
    return {
      modalVisible: false,
    };
  },
};
</script>

<style>
.mg {
  margin: 10px;
}
.asc:after {
  content: "\25B2"
}
.desc:after {
  content: "\25BC"
}
.highlight {
  font-weight: bold;
}
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}

.fade-enter {
  opacity:0;

}
</style>
