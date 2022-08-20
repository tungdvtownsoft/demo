<!-- eslint-disable vue/multi-word-component-names -->
<template>
  <div class="auth-form">
    <form @submit="submitForm">
      <p v-if="errors" class="invalid">
        {{ errors }}
      </p>
      <div class="flex">
        <label for="username" required>ID</label>
        <a-input :class="[
            errors && errors.search('username') !== -1 ? 'error' : '',
            'form-control',
          ]" name="username" v-model.lazy.trim="form.username" />
      </div>
      <div class="flex">
        <label for="password" required>Password</label>
        <a-input :class="[
            errors && errors.search('password') !== -1 ? 'error' : '',
            'form-control',
          ]" name="password" v-model.lazy.trim="form.password" />
      </div>
      <p v-if="errors && errors.error" class="invalid">
        {{ isSignup ? errors.error : "Invalid credentials" }}
      </p>
       <a-button type="primary"> {{
           signinLoading
            ? "Login in.."
            : "Login"
        }}</a-button>
    </form>
  </div>
</template>

<script>
import { reactive, ref } from "vue";
import { useMutation } from "@vue/apollo-composable";
import { useStore } from "vuex";
import { useRouter } from "vue-router";

import mutations from "../graphql/mutations/index";
export default {
  setup() {
    const store = useStore();
    const router = useRouter();
    const form = reactive({
      email: "",
      password: "",
      confirmpassword: "",
    });
    const isSignup = ref(false);
    const errors = ref(null);

    const submitForm = (e) => {
      e.preventDefault();
      signinData();
    };


    // signin mutation
    const {
      mutate: signinData,
      loading: signinLoading,
      onDone: signinDone,
      onError,
    } = useMutation(mutations.SIGN_IN, () => ({
      variables: {
        email: form.email,
        password: form.password,
      },
    }));
    signinDone((res) => {
      store.commit("auth/setAuthData", res.data.signin);
      router.push("/");
    });
    onError((error) => {
      errors.value = error.message;
    });

    return {
      form,
      isSignup,
      submitForm,
      errors,
      signinLoading,
    };
  },
};
</script>

<style scoped>
.auth-form {
    padding-top: 150px;
    max-width: 400px;
    margin: 0 auto;
}
.flex {
    display: flex;
    align-items: center;
    margin-left: -50px;
}
.flex label {
    width: 100px;
}
[required]::after {
  content: "*";
  color: red;
}
.form-control {
  margin: 8px 0;
 
}

.invalid:focus {
  background-color: salmon;
}
.error {
  background-color: salmon;
}
label {
  display: block;
}
.secondary {
  background-color: #ff0000;
  color: #ffffff;
}
.secondary:not(:disabled):hover {
  background: rgba(233, 12, 0, 0.75);
  color: #ffffff;
}
.invalid {
  color: red;
}
.primary {
  background-color: #065806;
  color: #ffffff;
}
.primary:hover {
  background-color: #45c545;
  color: #ffffff;
}
button:disabled {
  cursor: no-drop;
  background-color: silver;
  color: #000000;
}
</style>
