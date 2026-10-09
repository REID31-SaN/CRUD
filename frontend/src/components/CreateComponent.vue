<template>
  <div class="row justify-content-center">
    <div class="col-md-6">
      <h3 class="text-center mb-4">Add New Student</h3>

      <form @submit.prevent="handleSubmitForm">
        <div class="form-group mb-3">
          <label>Name</label>
          <input
            type="text"
            class="form-control"
            v-model.trim="student.name"
            required
          />
        </div>

        <div class="form-group mb-3">
          <label>Email</label>
          <input
            type="email"
            class="form-control"
            v-model.trim="student.email"
            required
          />
        </div>

        <div class="form-group mb-3">
          <label>Phone</label>
          <input
            type="tel"
            class="form-control"
            v-model.trim="student.phone"
            required
          />
        </div>

        <div class="form-group mb-3">
            <label>Address</label>
            <input
                type="text"
                class="form-control"
                v-model.trim="student.address"
                required
            />
        </div>

        <p v-if="message" class="text-success">
          {{ message }}
        </p>

        <p v-if="errorMessage" class="text-danger">
          {{ errorMessage }}
        </p>

        <div class="form-group mt-3">
          <button
            type="submit"
            class="btn btn-danger w-100"
            :disabled="isSubmitting"
          >
            {{ isSubmitting ? 'Creating...' : 'Create' }}
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
    data() {
    return {
        student: {
        name: '',
        email: '',
        phone: '',
        address: ''
        },
        isSubmitting: false,
        message: '',
        errorMessage: ''
    };
},

  methods: {
    async handleSubmitForm() {
      console.log('Create button clicked', this.student);

      this.isSubmitting = true;
      this.message = '';
      this.errorMessage = '';

      try {
        const apiURL = 'http://localhost:4000/api/create-student';

        const payload = {
          name: this.student.name,
          email: this.student.email,
          phone: Number(this.student.phone),
          address: this.student.address
        };

        const response = await axios.post(apiURL, payload);

        console.log('Student created:', response.data);

        this.message = 'Student created successfully!';

        this.student = {
          name: '',
          email: '',
          phone: '',
          address: ''
        };

        await this.$router.push('/view');
      } catch (error) {
        console.error('Create student error:', error);

        if (error.response) {
          this.errorMessage =
            error.response.data.message ||
            JSON.stringify(error.response.data) ||
            'The server rejected the request.';
        } else if (error.request) {
          this.errorMessage =
            'Cannot reach the backend. Check that your server is running on port 4000.';
        } else {
          this.errorMessage = error.message;
        }
      } finally {
        this.isSubmitting = false;
      }
    }
  }
};
</script>