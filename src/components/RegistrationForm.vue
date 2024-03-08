<template>
  <div class="registration">
    <div class="container">
      <form
        class="registration-form"
        @submit.prevent="submitHandler"
        novalidate="true"
        v-if="noSuccess"
      >
        <div class="registration-form__title">Регистрация</div>
        <hr />
        <div class="registration-form__wrap">
          <div class="registration-form__text">Заполните Ваши данные</div>
          <div class="registration-form__item">
            <div class="form-item">
              <input
                type="text"
                class="registration-form__input"
                :class="{
                  input__error: errors.name,
                }"
                v-model.trim="name"
                placeholder="Имя"
                autocomplete="off"
              />
              <small class="small small-errors" v-if="errors.name">{{
                errors.name
              }}</small>
            </div>
            <div class="form-item">
              <input
                type="email"
                :class="[
                  errors.email ? 'input__error' : '',
                  'registration-form__input',
                ]"
                v-model.trim="email"
                placeholder="Email"
                autocomplete="off"
              />
              <small class="small small-errors" v-if="errors.email">{{
                errors.email
              }}</small>
            </div>
          </div>
          <select
            class="registration-form__select"
            :class="{
              input__error: errors.role,
            }"
            v-model="selectedPost"
          >
            <option v-for="post in posts" :key="post.value" :value="post">
              {{ post.name }}
            </option>
          </select>

          <div class="registration-form__item">
            <div class="form-item">
              <input
                autocomplete="off"
                class="registration-form__input"
                :class="{
                  input__error: errors.password,
                }"
                v-model.trim="password"
                :type="isFlag ? 'password' : 'text'"
                placeholder="Пароль"
              />
              <button
                type="button"
                :class="[
                  isFlag ? 'input__eye' : ' input__eye_open',
                  'btn-outline-primary',
                ]"
                @click="clickBtn"
              ></button>
              <small class="small small-errors" v-if="errors.password">{{
                errors.password
              }}</small>
            </div>
            <div class="form-item">
              <input
                class="registration-form__input"
                v-model.trim="repeatPassword"
                :class="{
                  input__error: errors.repeatPassword,
                }"
                :type="isFlagCheck ? 'password' : 'text'"
                placeholder="Повторите пароль"
              />
              <button
                type="button"
                :class="[
                  isFlagCheck ? 'input__eye' : ' input__eye_open',
                  'btn-outline-primary',
                ]"
                @click="clickBtnPassword"
              ></button>
              <small class="small small-errors" v-if="errors.repeatPassword">{{
                errors.repeatPassword
              }}</small>
            </div>
          </div>
        </div>
        <hr />
        <div class="registration-form__elem">
          <div class="registration-form__switch">
            <div
              :class="[!isFlagSwitch ? '' : 'switch-on', 'switch-btn']"
              @click="switchBtn"
            ></div>
            <span>
              Хотите чтобы Ваш профиль видели другие участники платформы?
            </span>
          </div>
          <div class="registration-form__switch-text">
            Включает профиль для просмотра другими пользователями по ссылке
          </div>
          <div class="registration-form__checkbox-wrap">
            <div class="registration-form__checkbox">
              <input type="checkbox" v-model="checked" />
              Регистрируясь, Вы соглашаетесь с
              <span style="color: rgba(53, 134, 255, 1)"
                >политикой конфиденциальности</span
              >
              и обработкой
              <span style="color: rgba(53, 134, 255, 1)"
                >персональных данных</span
              >
            </div>
            <button
              type="submit"
              class="btn registration-form__btn-submit"
              :disabled="!checked"
            >
              Зарегистрироваться
            </button>
          </div>
        </div>
      </form>
      <h2 v-if="successTitle" class="success-title">
        Регистрация успешно завершена
      </h2>
    </div>
  </div>
</template>

<script>
export default {
  name: 'RegistrationForm',
  props: {},
  data() {
    return {
      posts: [
        {name: 'Front', value: 22},
        {name: 'Back', value: 25},
        {name: 'Engineer', value: 28},
      ],
      name: '',
      email: '',
      password: '',
      repeatPassword: '',
      isFlag: true,
      isFlagCheck: true,
      selectedPost: null,
      checked: true,
      isFlagSwitch: false,
      noSuccess: true,
      successTitle: false,
      errors: {
        name: null,
        email: null,
        role: null,
        password: null,
        repeatPassword: null,
      },
    };
  },

  methods: {
    clickBtn() {
      this.isFlag = !this.isFlag;
    },
    clickBtnPassword() {
      this.isFlagCheck = !this.isFlagCheck;
    },

    switchBtn() {
      this.isFlagSwitch = !this.isFlagSwitch;
    },
    submitHandler() {
      this.formIsValidEmail();
      this.formIsValidName();
      this.formIsValidSelect();
      this.formIsValidPassword();
      this.formIsValidPasswordCheck();

      if (
        this.formIsValidEmail() &&
        this.formIsValidName() &&
        this.formIsValidSelect() &&
        this.formIsValidPassword() &&
        this.formIsValidPasswordCheck()
      ) {
        let objDateSet = {
          public: this.isFlagSwitch,
          username: this.name,
          role: this.selectedPost.value,
          email: this.email,
          password: this.password,
          password_repeat: this.repeatPassword,
        };
        this.createPerson(objDateSet);
      }
    },
    async createPerson(objDate) {
      try {
        const response = await fetch(
          'https://vue-test-form-31cf7-default-rtdb.europe-west1.firebasedatabase.app/people.json',
          {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json',
            },
            body: JSON.stringify({
              ...objDate,
            }),
          }
        );
        const firebaseData = await response.json();
        console.log(objDate);
        console.log(firebaseData);
        if (response.ok) {
          this.name = '';
          this.isFlagSwitch = false;
          this.email = '';
          this.password = '';
          this.repeatPassword = '';
          this.selectedPost = '';
          this.noSuccess = false;
          this.successTitle = true;
        }
      } catch (error) {
        console.log(error);
      }
    },
    formIsValidEmail() {
      const res =
        /^([a-zA-Z][a-zA-Z0-9-_]{2,15})*@\w+([\\.-]?\w+)*(\.\w{2,3})+$/;
      let isValid = true;
      if (!res.test(this.email)) {
        this.errors.email = 'Укажите корректный адрес электронной почты.';
        isValid = false;
      } else {
        this.errors.email = null;
      }
      return isValid;
    },
    formIsValidName() {
      let isValid = true;
      if (this.name.length === 0) {
        this.errors.name = 'Укажите корректное имя';
        isValid = false;
      } else {
        this.errors.name = null;
      }
      return isValid;
    },
    formIsValidSelect() {
      let isValid = true;
      if (!this.selectedPost) {
        this.errors.role = 'Укажите должность';
        isValid = false;
      } else {
        this.errors.role = null;
      }
      return isValid;
    },
    formIsValidPassword() {
      const res = /^(?=.*\d)(?=.*[a-zA-Z])[a-zA-Z0-9]{7,}$/;
      let isValid = true;
      if (!res.test(this.password)) {
        this.errors.password =
          'Укажите пароль латинские буквы и цифры больше 6';
        isValid = false;
      } else {
        this.errors.password = null;
      }
      return isValid;
    },
    formIsValidPasswordCheck() {
      let isValid = true;
      if (this.password !== this.repeatPassword) {
        this.errors.repeatPassword = 'Пароли не совпадают';
        isValid = false;
      } else if (!this.repeatPassword) {
        this.errors.repeatPassword = 'Поле не может быть пустым';
        isValid = false;
      } else {
        this.errors.repeatPassword = null;
      }
      return isValid;
    },
  },
};
</script>
<style scoped lang="scss">
.registration {
  backdrop-filter: blur(6px);
  background-color: rgba(121, 156, 212, 0.5);
  width: 100%;
  justify-content: center;
  align-items: center;
  padding-top: 50px;
  background-image: url(@/assets/img/blur.jpg);
  background-repeat: no-repeat;
  background-size: cover;
  height: 100vh;
}
.registration__wrap {
  border-radius: 2px;
  box-shadow: 0px 2px 8px 0px rgba(0, 0, 0, 0.15);
  background-color: #fff;
  display: flex;
  max-width: 100%;
  flex-direction: column;
  margin: 90px 0 64px;
  padding: 50px 87px;
}
hr {
  background-color: #d9d9d9;
  border: 0;
  color: #d9d9d9;
  height: 1px;
  margin: 0;
  width: 100%;
}
.registration-form {
  border-radius: 15px;
  background-color: #fff;
  display: flex;
  max-width: 960px;
  flex-direction: column;
  padding: 19px 0 66px;
  margin: 0 auto;
}
.registration-form__title {
  color: #000;
  letter-spacing: -0.07px;
  align-self: start;
  margin-left: 31px;
  margin-bottom: 11px;
  font: 700 19px/142% Montserrat, sans-serif;
}
@media (max-width: 991px) {
  .registration {
    height: 100%;
  }
  .registration-form__title {
    margin-left: 10px;
  }
  .registration-form {
    max-width: 100%;
    padding: 0;
  }
}

.registration-form__wrap {
  display: flex;
  margin-top: 20px;
  flex-direction: column;
  font-size: 14px;
  color: #9292a0;
  font-weight: 400;
  line-height: 136%;
  padding: 0 15px 0 31px;
  margin-bottom: 30px;
}
@media (max-width: 991px) {
  .registration-form__wrap {
    max-width: 100%;
    padding-left: 20px;
  }
}
.registration-form__text {
  color: #000;
  font: 500 16px/119% Montserrat, sans-serif;
}
@media (max-width: 991px) {
  .registration-form__text {
    max-width: 100%;
  }
}
.registration-form__item {
  display: flex;
  margin-top: 36px;
  gap: 14px;
}
@media (max-width: 991px) {
  .registration-form__item {
    max-width: 100%;
    flex-wrap: wrap;
    white-space: initial;
  }
}
.registration-form__input {
  font-family: Montserrat, sans-serif;
  border-radius: 11px;
  flex-grow: 1;
  padding: 10px;
  border: 1px solid rgba(230, 230, 235, 1);
  width: 95%;
}
@media (max-width: 991px) {
  .registration-form__input {
    max-width: 100%;
    padding-right: 20px;
    white-space: initial;
  }
}

.registration-form__select {
  border-radius: 11px;
  border-color: rgba(230, 230, 235, 1);
  border-style: solid;
  border-width: 1px;
  align-self: end;
  display: flex;
  margin-top: 31px;
  width: 450px;
  max-width: 100%;
  justify-content: space-between;
  gap: 20px;
  white-space: nowrap;
  letter-spacing: -0.02px;
  padding: 10px;
  margin-bottom: -5px;
}
@media (max-width: 991px) {
  .registration-form__select {
    width: 100%;
  }
}

.registration-form__elem {
  display: flex;
  margin-top: 21px;
  flex-direction: column;
  align-items: start;
  padding: 0 15px 0 29px;
}
@media (max-width: 991px) {
  .registration-form__elem {
    max-width: 100%;
    padding: 0 20px;
  }
}

.registration-form__switch {
  color: #000;
  display: flex;
  text-align: center;
  font: 500 16px/119% Montserrat, sans-serif;
}
.registration-form__switch span {
  margin-left: 8px;
}
@media (max-width: 991px) {
  .registration-form__switch {
    max-width: 100%;
  }
}
@media (max-width: 700px) {
  .registration-form__switch span {
    max-width: 80%;
  }
}
.registration-form__switch-text {
  color: #696977;
  letter-spacing: -0.02px;
  margin: 15px 0 0 51px;
  font: 400 14px/136% Montserrat, sans-serif;
}
@media (max-width: 991px) {
  .registration-form__switch-text {
    max-width: 100%;
  }
}
.registration-form__checkbox-wrap {
  align-self: stretch;
  display: flex;
  margin-top: 24px;
  width: 100%;
  justify-content: space-between;
  gap: 20px;
  font-weight: 400;
  letter-spacing: -0.02px;
}
@media (max-width: 991px) {
  .registration-form__checkbox-wrap {
    max-width: 100%;
    flex-wrap: wrap;
  }
}

.registration-form__checkbox {
  font-family: Montserrat, sans-serif;
  flex-grow: 1;
  max-width: 65%;
  flex-basis: auto;
}
@media (max-width: 991px) {
  .registration-form__checkbox {
    max-width: 100%;
  }
}
.registration-form__btn-submit {
  border-radius: 8px;
  background-color: rgba(73, 122, 218, 0.2);
  justify-content: center;
  align-items: center;
  color: #497ada;
  white-space: nowrap;
  flex-grow: 1;
  padding: 12px 60px;
  font: 12px/158% Montserrat, sans-serif;
}
@media (max-width: 991px) {
  .registration-form__btn-submit {
    margin-bottom: 20px;
  }
}

.input__eye::after {
  content: url('@/assets/svg/hided.svg');
}
.input__eye_open {
  &::after {
    content: url('@/assets/svg/showed.svg');
  }
}
.btn-outline-primary {
  position: absolute;
  top: 10px;
  right: 16px;
  width: 20px;
  height: 20px;
}
.small {
  padding-left: 12px;
  font-weight: 500;
  font-size: 12px;
  line-height: 16px;
  letter-spacing: 0.2px;
  color: rgb(167, 167, 167);
}
.small-errors {
  color: rgb(244, 44, 79);
}

.form-item {
  width: 100%;
  position: relative;
}
.switch-btn {
  display: inline-block;
  width: 43px;
  height: 19px;
  border-radius: 19px;
  background: #bfbfbf;
  z-index: 0;
  margin: 0;
  padding: 0;
  border: none;
  cursor: pointer;
  position: relative;
  transition-duration: 300ms;
}
.switch-btn::after {
  content: '';
  height: 19px;
  width: 19px;
  border-radius: 17px;
  background: #fff;
  top: 0px;
  left: 0px;
  transition-duration: 300ms;
  position: absolute;
  z-index: 1;
}
.switch-on {
  background: #3586ff;
}
.switch-on::after {
  left: 24px;
}

.btn:hover {
  cursor: pointer;
  opacity: 0.8;
}

.btn:disabled {
  cursor: not-allowed;
  opacity: 1 !important;
  background: #eee !important;
  border-color: #ddd !important;
  color: #999 !important;
}

.btn:active {
  box-shadow: inset 1px 1px 1px rgba(0, 0, 0, 0.3);
}

.btn.primary {
  background: #42b983;
  color: #fff;
}

.btn.danger {
  background: #e53935;
  color: #fff;
  border-color: #e53935;
}

.btn.warning {
  background: #c25205;
  color: #fff;
  border-color: #c25205;
}
.input__error {
  border: 1px solid rgb(244, 44, 79);
}
.success-title {
  color: #fff;
  margin: 0;
  margin-bottom: 70px;
  font-weight: 700;
  font-size: 46px;
  line-height: 130%;
  text-align: center;
}
@media (max-width: 400px) {
  .form-item {
    width: 95%;
  }
}
</style>

