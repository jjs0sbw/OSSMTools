<template>
  <div>
    <Header />

    <div class="container">

    <ul class="nav nav-pills nav-fill mb-3" id="pills-tab" role="tablist">
        <li class="nav-item">
            <a class="nav-link active" id="pills-login-tab" data-toggle="pill" href="#pills-login" role="tab" aria-controls="pills-upload" aria-selected="true"><b>Log In</b></a>
        </li>
        <li class="nav-item">
            <a class="nav-link" id="pills-register-tab" data-toggle="pill" href="#pills-register" role="tab" aria-controls="pills-verify" aria-selected="false"><b>Register</b></a>
        </li>
    </ul>

    <div class="tab-content" id="pills-tabContent">
        <div class="tab-pane fade show active" id="pills-login" role="tabpanel" aria-labelledby="pills-login-tab">
            <div class="row">
                <div class="col-md-5">
                    <form @submit.prevent="login">
                        <div class="form-group">
                            <label for=""><b>Email:</b></label>
                            <input type="email" required class="form-control" placeholder="eg participant@structuralmodeling.com" v-model="model.email">
                        </div>

                        <div class="form-group">
                            <label for=""><b>Password:</b></label>
                            <input type="password" required class="form-control" placeholder="Enter Password" v-model="model.password">
                        </div>

                        <div class="form-group">
                            <button class="btn btn-primary" ><b>Login</b></button>
                            {{ loading }}
                            {{ status }}
                        </div>
                    </form>
                </div>
            </div>
        </div>
        <div class="tab-pane fade" id="pills-register" role="tabpanel" aria-labelledby="pills-register-tab">
            <div class="row">
                <div class="col-md-5">
                    <form @submit.prevent="register">
                        <div class="form-group">
                            <label for=""><b>Name:</b></label>
                            <input type="text" required class="form-control" placeholder="eg Participant" v-model="model.name">
                        </div>

                        <div class="form-group">
                            <label for="">Email:</label>
                            <input type="email" required class="form-control" placeholder="eg participant@structuralmodeling.com" v-model="model.email">
                        </div>

                        <div class="form-group">
                            <label for=""><b>Company Name:</b></label>
                            <input type="text" required class="form-control" placeholder="eg System Tech" v-model="model.company_name">
                        </div>
                        <div class="form-group">
                            <label for=""><b>Password:</b></label>
                            <input type="password" required class="form-control" placeholder="Enter Password" v-model="model.password">
                        </div>
                        <div class="form-group">
                            <label for=""><b>Confirm Password:</b></label>
                            <input type="password" required class="form-control" placeholder="Confirm Password" v-model="model.c_password">
                        </div>

                        <div class="form-group">
                            <button class="btn btn-primary" ><b>Register</b></button>
                            {{ loading }}
                            {{ status }}
                        </div>
                    </form>
                </div>
            </div>
        </div>
      </div>
    </div>
  </div>

</template>

<script>
import Header from "./Header";
import axios from "axios";

export default {
  name: "SignUp",
  components: {
    Header
  },
  data() {
    return {
      model: {
        name: "",
        email: "",
        password: "",
        c_password: "",
        company_name: ""
      },
      loading: "",
      status: ""
    };
  },
  methods: {
    validate() {
      // checks all the form params are set and the passwords match
      if (this.model.password != this.model.c_password) {
        return false;
      }

      return true;
    },
    register() {
      const formData = new FormData();
      let valid = this.validate();
      if (valid) {
        formData.append("name", this.model.name);
        formData.append("email", this.model.email);
        formData.append("company_name", this.model.company_name);
        formData.append("password", this.model.password);

        this.loading = "Registering you, please wait";
        // Post to server
        axios.post("http://localhost:3128/register", formData).then(res => {
          // Post a status message
          this.loading = "";
          if (res.data.status == true) {
            // store the data in localStorage
            localStorage.setItem("token", res.data.token);
            localStorage.setItem("user", JSON.stringify(res.data.user));
            // now send the user to the next route
            this.$router.push({
              name: "Dashboard"
            });
          } else {
            this.status = res.data.message;
          }
        });
      } else {
        alert("Passwords do not match");
      }
    },
    login() {
      const formData = new FormData();
      formData.append("email", this.model.email);
      formData.append("password", this.model.password);

      this.loading = "Signing in";
      // Post to server
      axios.post("http://localhost:3128/login", formData).then(res => {
        // Post a status message
        console.log(res);
        this.loading = "";
        if (res.data.status == true) {
          // store the data in localStorage
          localStorage.setItem("token", res.data.token);
          localStorage.setItem("user", JSON.stringify(res.data.user));

          // now send the user to the next route
          this.$router.push({
            name: "Dashboard"
          });
        } else {
          this.status = res.data.message;
        }
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #426cb9;
}
</style>
