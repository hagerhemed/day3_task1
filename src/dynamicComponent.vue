<template>
  <div class="d-flex w-50 text-center mx-auto my-2 py-2">
    <ul class="breadcrumb w-50 text-center mx-auto">
        <li class="breadcrumb-item"><a href="#" @click.prevent="targetComponent = 'booksComponent'">Books</a></li>
        <li class="breadcrumb-item"><a href="#" @click.prevent="targetComponent = 'productComponent'">Products</a></li>
    </ul>
    <div>
        <div>
        <component :is="targetComponent"/>
    </div>
    </div>
  </div>
</template>

<script>
import booksComponent from './components/booksComponent';
import productComponent from './components/productComponent';

export default {
  components: {
    booksComponent,
    productComponent,
  },
  data() {
    return {
      targetComponent: 'booksComponent',
    };
  },
};
</script>

<style>

</style>