<template>
  <Form @submit="submitContact" :validation-schema="contactFormSchema">
    <div class="form-group">
      <label for="name">Tên</label>
      <Field name="name" type="text" class="form-control" v-model="contactLocal.name" />
      <ErrorMessage name="name" class="error-feedback" />
    </div>

    <div class="form-group">
      <label for="email">E-mail</label>
      <Field name="email" type="email" class="form-control" v-model="contactLocal.email" />
      <ErrorMessage name="email" class="error-feedback" />
    </div>

    <div class="form-group">
      <label for="address">Địa chỉ</label>
      <Field name="address" type="text" class="form-control" v-model="contactLocal.address" />
      <ErrorMessage name="address" class="error-feedback" />
    </div>

    <div class="form-group">
      <label for="phone">Điện thoại</label>
      <Field name="phone" type="tel" class="form-control" v-model="contactLocal.phone" />
      <ErrorMessage name="phone" class="error-feedback" />
    </div>

    <div class="form-group form-check">
      <input name="favorite" type="checkbox" class="form-check-input" v-model="contactLocal.favorite" />
      <label for="favorite" class="form-check-label">
        <strong>Liên hệ yêu thích</strong>
      </label>
    </div>

    <div class="form-group">
      <label><strong>Có sở thích?</strong></label>

      <div>
        <label class="mr-3">
          <input
            type="radio"
            :value="true"
            v-model="contactLocal.hasHobby"
          />
          Có
        </label>

        <label class="ml-3">
          <input
            type="radio"
            :value="false"
            v-model="contactLocal.hasHobby"
          />
          Không
        </label>
      </div>
    </div>

    <div class="form-group" v-if="contactLocal.hasHobby">
      <label><strong>Chọn sở thích</strong></label>

      <div class="form-check">
        <input
          class="form-check-input"
          type="checkbox"
          value="Xã hội"
          v-model="contactLocal.hobbies"
        />
        <label class="form-check-label">Xã hội</label>
      </div>

      <div class="form-check">
        <input
          class="form-check-input"
          type="checkbox"
          value="Khoa học"
          v-model="contactLocal.hobbies"
        />
        <label class="form-check-label">Khoa học</label>
      </div>

      <div class="form-check">
        <input
          class="form-check-input"
          type="checkbox"
          value="Công nghệ"
          v-model="contactLocal.hobbies"
        />
        <label class="form-check-label">Công nghệ</label>
      </div>

      <div class="form-check">
        <input
          class="form-check-input"
          type="checkbox"
          value="Giải trí"
          v-model="contactLocal.hobbies"
        />
        <label class="form-check-label">Giải trí</label>
      </div>
    </div>

    <div class="form-group">
      <button class="btn btn-primary">Lưu</button>
      <button v-if="contactLocal._id" type="button" class="ml-2 btn btn-danger" @click="deleteContact">
        Xóa
      </button>
      <button type="button" class="ml-2 btn btn-danger" @click="Cancel">
        Thoát
      </button>
    </div>
  </Form>
</template>

<script>
import * as yup from "yup";
import { Form, Field, ErrorMessage } from "vee-validate";

export default {
  components: {
    Form,
    Field,
    ErrorMessage,
  },
  emits: ["submit:contact", "delete:contact"],
  props: {
    contact: { type: Object, required: true }
  },
  data() {
    const contactFormSchema = yup.object().shape({
      name: yup
        .string()
        .required("Tên phải có giá trị.")
        .min(2, "Tên phải ít nhất 2 ký tự.")
        .max(50, "Tên có nhiều nhất 50 ký tự."),
      email: yup
        .string()
        .email("E-mail không đúng.")
        .max(50, "E-mail tối đa 50 ký tự."),
      address: yup.string().max(100, "Địa chỉ tối đa 100 ký tự."),
      phone: yup
        .string()
        .matches(
          /((09|03|07|08|05)+([0-9]{8})\b)/g,
          "Số điện thoại không hợp lệ."
        ),
    });
    return {
      // Sử dụng destructuring {...} để tạo bản sao clone, tránh làm thay đổi trực tiếp props cha
      contactLocal: { 
        hobbies: [],
        hasHobby: false,
        ...this.contact },
      contactFormSchema,
    };
  },
  methods: {
    submitContact() {
      this.$emit("submit:contact", this.contactLocal);
    },
    deleteContact() {
         if (this.contactLocal.hasHobby) {

        if (
          !this.contactLocal.hobbies ||
          this.contactLocal.hobbies.length === 0
        ) {
          alert("Bạn phải chọn ít nhất một sở thích.");
          return;
        }

      } else {

        this.contactLocal.hobbies = [];

      }

      // Đồng nhất sử dụng _id giống như v-if ngoài template
      this.$emit("delete:contact", this.contactLocal._id);
    },
    Cancel() {
      const reply = window.confirm('You have unsaved changes! Do you want to leave?');
      if (!reply) {
        return false;
      } else {
        this.$router.push({ name: "contactbook" });
      }
    }
  },
};
</script>

<style scoped>
@import "@/assets/form.css";
</style>