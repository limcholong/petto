<template>
  <member-login-form @submit="onSubmit" />
</template>

<script>
import axios from "axios";
import { mapActions, mapState } from "vuex";
import MemberLoginForm from "@/components/member/MemberLoginForm.vue";

export default {
  name: "MemberLoginPage",
  components: {
    MemberLoginForm,
  },
  data() {
    return {
      isLogin: false,
    };
  },
  mounted() {
    this.$store.state.session = this.$cookies.get("user");
    if (this.$store.state.session != null) {
      this.isLogin = true;
    }
  },
  computed: {
    ...mapState(["session"]),
  },
  methods: {
    ...mapActions(["fetchSession", 'updateAlarmList']),

    onSubmit(payload) {
      if (this.$store.state.session == null) {
        const { id, password } = payload;
        axios
          .post("http://localhost:8888/petto/member/login", { id, password })
          .then(res => {
            if (res.data != "") {

              alert("로그인 성공! - " + JSON.stringify(res.data));
          
              this.isLogin = true;
              this.$store.state.session = res.data;
              delete this.session.updateAlarmList;

              this.$cookies.set("user", res.data, "12h");
              this.$router.push({name: 'PettoHome'})

            } else {
              alert("로그인 실패! - " + res.data);
              this.isLogin = false;
            }
          })
          .catch(res => {
            alert(res.response.data.message);
          });
      } else {
        alert(
          "이미 로그인 되어 있습니다 - 계정: " + this.$store.state.session.id
        );
      }
    },
    
    logout() {
      this.$cookies.remove("user");
      this.isLogin = false;
      this.$store.state.session = null;
    },
  },
};
</script>
