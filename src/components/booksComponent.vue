<template>
  <div class="container">
    <h1 class="text-center my-5">Book List</h1>
    <div class="row row-cols-1 row-cols-md-2 row-cols-lg-2 g-4">
      <div v-for="book in books" :key="book.id" class="col">
        <div class="card">
          <img :src="book.image" alt="image" class="card-img-top">
          <div class="card-body">
            <h5 class="card-title">Name: {{ book.name }}</h5>
            <p class="card-text">Pages: {{ book.pages }} pages</p>
            <p class="card-text">Price: {{ book.price }}</p>
            <p class="card-text">Category: {{ book.category }}</p>
            <div class="d-flex justify-content-around">
              <button @click="editBook(book)" class="btn btn-primary">Edit</button>
              <button @click="deleteBook(book.id)" class="btn btn-danger">Delete</button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Edit Book Modal -->
    <div class="modal" v-if="editingBook">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Edit Book</h5>
            <button type="button" class="btn-close" @click="cancelEdit"></button>
          </div>
          <div class="modal-body">
            <form @submit.prevent="saveEdit">
              <div class="mb-3">
                <label for="edit-name" class="form-label">Name</label>
                <input type="text" id="edit-name" v-model="editingBook.name" class="form-control" required>
              </div>
              <div class="mb-3">
                <label for="edit-pages" class="form-label">Pages</label>
                <input type="number" id="edit-pages" v-model="editingBook.pages" class="form-control" required>
              </div>
              <div class="mb-3">
                <label for="edit-price" class="form-label">Price</label>
                <input type="number" id="edit-price" v-model="editingBook.price" class="form-control" required>
              </div>
              <div class="mb-3">
                <label for="edit-category" class="form-label">Category</label>
                <input type="text" id="edit-category" v-model="editingBook.category" class="form-control" required>
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
      let res = await fetch("http://localhost:5000/books");
      this.books = await res.json();
    } catch (error) {
      console.error(error);
    }
  },
  data() {
    return {
      books: [],
      editingBook: null,
    };
  },
  methods: {
    async deleteBook(bookId) {
      try {
        await fetch(`http://localhost:5000/books/${bookId}`, {
          method: 'DELETE',
        });
        this.books = this.books.filter((book) => book.id !== bookId);
      } catch (error) {
        console.error(error);
      }
    },
    editBook(book) {
      this.editingBook = { ...book };
    },
    cancelEdit() {
      this.editingBook = null;
    },
    async saveEdit() {
      try {
        const response = await fetch(`http://localhost:5000/books/${this.editingBook.id}`, {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(this.editingBook),
        });
        if (response.ok) {
          const updatedBook = await response.json();
          const index = this.books.findIndex((book) => book.id === updatedBook.id);
          if (index !== -1) {
            this.books.splice(index, 1, updatedBook);
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
</style>