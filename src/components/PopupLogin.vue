<template>
  <div class="popup-login-container">
    <div>
      <div class="popup-home text-center mb-15" v-on:click="clickHome"><img v-bind:src="logoText"/></div>
      <div class="text-center text-white font-size-24 font-weight-600 mb-8">돌아오신 것을 환영해요!</div>
      <div class="text-center font-size-16">다시 만나다니 너무 반가워요!</div>
    </div>
    <div class="mt-20">
      <label for="userId" class="mb-5 d-block">
                <span class="d-block font-size-13" v-bind:class="{ 'text-red': $v.param.userId.$error }"
                >아이디
                    <span class="font-size-13 text-italic" v-if="$v.param.userId.$error">{{
                        validationMessages.userId.required
                      }}</span></span
                >
      </label>
      <input
          type="text"
          id="userId"
          name="userId"
          v-model="param.userId"
          class="w-100-per form-control bg-dark border-dark mb-15 text-white"
      />
      <label for="userPw" class="d-block mb-5">
                <span class="d-block font-size-13" v-bind:class="{ 'text-red': $v.param.userPw.$error }"
                >비밀번호
                    <span class="font-size-13 text-italic" v-if="$v.param.userPw.$error">{{
                        validationMessages.userPw.required
                      }}</span></span
                >
      </label>
      <input
          type="password"
          id="userPw"
          name="userPw"
          v-model="param.userPw"
          class="w-100-per form-control bg-dark border-dark text-white mb-5"
          v-on:keypress.enter="clickLogin"
      />
      <div class="mb-15 text-info cursor-pointer font-size-13" v-on:click="forgetPW">비밀번호를 잊으셨나요?</div>
      <a v-bind:href="loginUrl" class="d-block w-100-per btn btn-primary mb-8 cursor-pointer text-center"
      >구글 계정으로 로그인</a
      >
      <div class="font-size-13">
                <span
                >계정이 필요한가요?<span class="text-info ml-5 cursor-pointer" v-on:click="clickRegister"
                >가입하기</span
                ></span
                >
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Emit, Mixins } from 'vue-property-decorator';
import validationsMix from '@/shared/mixins/validationMix';
import { validations, validationsMessage } from '@/components/viewData/PopupLogin';
import { IBaseTooltip } from '@/shared/interface/ICommon';

@Component({
  validations: {
    param: {
      ...validations,
    },
  },
})
export default class PopupLogin extends Mixins(validationsMix) {
  private param: any = {};
  private loginUrl: string = '';
  private color: string[] = [
    'text-cyan',
    'text-info',
    'text-red',
    'text-gray',
    'text-blue',
    'text-orange',
    'text-purple',
    'text-brown',
    'text-salmon',
  ];

  get logoText(): any {
    return require('../assets/img/logotext.png');
  }

  get validationGroup(): any {
    return this.$v.param;
  }

  get validationMessages(): any {
    return validationsMessage;
  }

  get defaultToolTip(): IBaseTooltip {
    return {
      trigger: 'manual',
      show: false,
      placement: 'left',
      content: '',
      classes: ['popover', 'fade', 'in', 'left', 'top-minus-6', 'm-r-5'],
    };
  }

  private mounted() {
    this.$axios
        .get('/api/check')
        .then((rs: any) => {
          this.$store.commit('setAuth', {
            name: rs.data.name,
            givenName: rs.data.given_name,
            picture: rs.data.picture,
          });

          this.$router.push('/channels');
          return;
        })
        .catch((e: any) => {
          const url: string = '/api/login';
          this.$axios.get(url).then((rs: any) => {
            this.loginUrl = rs.data;
          });
        });
  }

  private clickHome(): void {
    this.$router.push('/');
  }

  private clickRegister(): void {
    this.$router.push('/register');
  }

  private clickLogin(): void {
    this.$v.$touch();
    alert('가입미구현, 구글로그인을 이용하세요.');
    // if (!this.$v.$error) {
    //   localStorage.setItem('userId', this.param.userId);
    //   const idx = Math.floor(Math.random() * this.color.length);
    //   localStorage.setItem('userColor', this.color[idx]);
    //   this.$router.push('/channels');
    // }
  }

  private forgetPW(): void {
    this.$v.param.userId?.$touch();
  }
}
</script>