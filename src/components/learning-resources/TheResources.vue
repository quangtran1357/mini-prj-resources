<template>
  <base-card>
    <base-button
      @click="setSelectedTab('stored-resources')"
      :mode="storedResButtonMode"
    >Stored Resources</base-button>
    <base-button @click="setSelectedTab('add-resource')" :mode="addResButtonMode">Add Resource</base-button>
  </base-card>
  <keep-alive>
    <component :is="selectedTab" :resources="initResources"></component>
  </keep-alive>
</template>

<script>
// import {computed} from 'vue';
import StoredResources from './StoredResources.vue';
import AddResource from './AddResource.vue';

export default {
  components: {
    StoredResources,
    AddResource,
  },
  provide() {
    return {
      addResource: this.addResource,
      deleteResource: this.removeResource
    };
  },
  data() {
    return {
      selectedTab: 'stored-resources',
      initResources: [],
    };
  },
  computed: {
    storedResButtonMode() {
      return this.selectedTab === 'stored-resources' ? null : 'flat';
    },
    addResButtonMode() {
      return this.selectedTab === 'add-resource' ? null : 'flat';
    },
  },
  methods: {
    getLocalStorage(name) {
      return JSON.parse(localStorage.getItem(name))
    },
    setLocalStorage(name, data) {
      localStorage.setItem(name, JSON.stringify(data));
    },
    setSelectedTab(tab) {
      this.selectedTab = tab;
    },
    addResource(title, description, url) {
      const newResource = {
        id: new Date().toISOString(),
        title: title,
        description: description,
        link: url,
      };
      this.initResources.unshift(newResource);
      this.setLocalStorage('resources', this.initResources);
      this.initResources = this.getLocalStorage('resources');
      this.selectedTab = 'stored-resources';
    },
    removeResource(resId) {
      const resIndex = this.initResources.findIndex(res => res.id === resId);
      this.initResources.splice(resIndex, 1);
      this.setLocalStorage('resources', this.initResources);
      this.initResources = this.getLocalStorage('resources');
    }
  },
  mounted() {
    const resources = this.getLocalStorage('resources');
    if (resources && resources.length) {
      this.initResources = resources;
    } else {
      this.setLocalStorage('resources', []);
    }
  }
};
</script>
