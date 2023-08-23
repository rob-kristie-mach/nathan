<template>
    <div class="component-section">
      <section :class="[data.elements.padding.value[0].codename]">
       
        <div v-for="nestedComponent in nestedComponents" :key="nestedComponent.componentCodename">
         <p> Nested Component is: {{ componentName(nestedComponent) }} </p>
          <component :is="componentName(nestedComponent)" :data="nestedComponent.data" :nestedComponents="getNestedComponents(nestedComponent)" />
        </div>
        
      </section>
    </div>
  </template>
  
  <script setup>
import { defineProps } from 'vue';

const props = defineProps({
  data: {
    type: Object,
    required: true,
  },
  nestedComponents: {
    type: Array,
    default: () => [],
  },
});

// Get the component name based on the component type
const componentName = (item) => {
  const type = item.componentType;
  const componentName = type
    .replace(/__/g, '-')
    .split('-')
    .map((word) => word.charAt(0).toUpperCase() + word.slice(1))
    .join('');
  return componentName;
};

// Get nested components for a given item
const getNestedComponents = (item) => {
  console.log(item); // Log the entire item to inspect its structure
  if (!item.data || !item.data.elements || !item.data.elements.components) {

    return [];
  }

  const nestedComponents = item.data.elements.components.linkedItems;
  return nestedComponents.map((component) => ({
    componentType: component.system.type,
    componentCodename: component.system.codename,
    data: component.elements,
  }));
};
</script>
  
  <style scoped>
  /* Add your styles here */
  </style>
  