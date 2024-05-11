<template>
  <div class="container">
    <h1 class="text-center my-5">Products List</h1>
    <div class="row row-cols-1 row-cols-md-2 row-cols-lg-2 g-4">
      <div v-for="product in products" :key="product.id" class="col">
        <div class="card">
          <img :src="product.image" alt="image" class="card-img-top">
          <div class="card-body">
            <h5 class="card-title text-truncate">Title: {{ product.title }}</h5>
            <p class="card-text text-truncate">Price: {{ product.price }}</p>
            <div class="d-flex justify-content-around">
              <button @click="editProduct(product)" class="btn btn-primary">Edit</button>
              <button @click="deleteProduct(product.id)" class="btn btn-danger">Delete</button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div v-if="editingProduct" class="modal">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Edit Product</h5>
            <button type="button" class="btn-close" @click="cancelEdit"></button>
          </div>
          <div class="modal-body">
            <form @submit.prevent="saveEdit">
              <div class="mb-3">
                <label for="edit-title" class="form-label">Title</label>
                <input type="text" id="edit-title" v-model="editingProduct.title" class="form-control" required>
              </div>
              <div class="mb-3">
                <label for="edit-price" class="form-label">Price</label>
                <input type="number" id="edit-price" v-model="editingProduct.price" class="form-control" required>
              </div>
              <div class="modal-footer">
                <button type="button" class="btn btn-secondary" @click="cancelEdit">Cancel</button>
                <button type="submit" class="btn btn-primary">Save Changes</button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  async created() {
    try {
      let res = await fetch("http://localhost:5000/products");
      this.products = await res.json();
    } catch (error) {
      console.error(error);
    }
  },
  data() {
    return {
      products: [],
      editingProduct: null,
    };
  },
  methods: {
    async deleteProduct(productId) {
      try {
        await fetch(`http://localhost:5000/products/${productId}`, {
          method: 'DELETE',
        });
        this.products = this.products.filter((product) => product.id !== productId);
      } catch (error) {
        console.error(error);
      }
    },
    editProduct(product) {
      this.editingProduct = { ...product };
      document.body.classList.add('modal-open'); 
    },
    cancelEdit() {
      this.editingProduct = null;
      document.body.classList.remove('modal-open'); 
    },
    async saveEdit() {
      try {
        const response = await fetch(`http://localhost:5000/products/${this.editingProduct.id}`, {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(this.editingProduct),
        });
        if (response.ok) {
          const updatedProduct = await response.json();
          const index = this.products.findIndex((product) => product.id === updatedProduct.id);
          if (index !== -1) {
            this.products.splice(index, 1, updatedProduct);
          }
        }
        this.cancelEdit();
      } catch (error) {
        console.error(error);
      }
    },
  },
};
</script>

<style>
.modal {
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-dialog {
  max-width: 500px;
  background-color: #fff;
  border-radius: 5px;
}

.modal-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
</style>