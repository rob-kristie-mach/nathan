<template>
    <div>

     <p>Nested Components Are: </p>
      <div v-for="item in linkedItems" :key="item.system.codename">
        <p>Main Component Name = {{ componentName(item) }}</p>
        <!-- Use PascalCase for component names -->
        <component :is="componentName(item)" :data="item" :nestedComponents="getNestedComponents(item)" />
        
      </div>
      <p>Full JSON is:</p>
      <p>{{ pageData }}</p>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import { createDeliveryClient } from '@kontent-ai/delivery-sdk';
  
  const deliveryClient = createDeliveryClient({
    environmentId: 'd21c04b3-d8e3-0069-0344-815518ff2064',
  });
  
  const pageData = ref({});
  const linkedItems = ref([]);
  
  const fetchPageData = async () => {
    try {
      const response = await deliveryClient.items('nathan_home_page')
      .collection('nathanark')
      .toPromise();
      pageData.value = response;
      linkedItems.value = response.data.items[0].elements.components.linkedItems;
    } catch (error) {
      console.error('Error fetching data:', error);
    }
  };
  
  onMounted(fetchPageData);
  
  const componentName = (item) => {
    const type = item.system.type;
    const componentName = type
      .replace(/__/g, '-')
      .split('-')
      .map((word) => word.charAt(0).toUpperCase() + word.slice(1))
      .join('');
    return componentName;
  };
  
  const getNestedComponents = (item) => {
    const richTextElement = item.elements.components;
    if (!richTextElement || !richTextElement.linkedItems) {
      return [];
    }
  
    const nestedComponents = richTextElement.linkedItems;
    return nestedComponents.map((component) => ({
      componentType: component.system.type,
      componentCodename: component.system.codename,
      data: component.elements,
    }));
  };
  
  const head = () => ({
    title: pageData.value.elements.meta_title.value,
    meta: [
      {
        name: "description",
        property: "description",
        content: pageData.value.elements.meta_description.value,
      },
      {
        property: "og:title",
        content: pageData.value.elements.og_title.value,
      },
      {
        property: "og:description",
        content: pageData.value.elements.og_title.value,
      },
    ],
  });
  </script>

  