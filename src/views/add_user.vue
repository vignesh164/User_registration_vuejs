<template>
    <b-modal id="model" centered size="md" v-model="visible"
             title="Add User" hide-footer>
        <transition :name="transition_name" mode="out-in">
            <b-card v-if="current_step===1" v-bind:key="5" align="left">
                <b-card-text>
                    <label for="first_name" style="margin-bottom: 0px; font-size: 14px;">
                        First Name:
                    </label>
                    <b-form-input id="first_name" v-model="user_data.first_name" size="sm" name="first_name"
                                  type="text" required v-validate="'required'"
                                  :state="validateState('first_name')" data-vv-name="first_name">
                    </b-form-input>
                    <label for="last_name" style="margin-bottom: 0px; font-size: 14px;">
                        Last Name:
                    </label>
                    <b-form-input id="last_name" v-model="user_data.last_name" size="sm" name="last_name"
                                  type="text" required v-validate="'required'"
                                  :state="validateState('last_name')" data-vv-name="last_name">
                    </b-form-input>
                    <label for="date_of_birth" style="margin-bottom: 0px; font-size: 14px;">
                        Date of Birth:
                    </label>
                    <b-form-datepicker id="date_of_birth" size="sm" local="en" required v-model="user_data.user_details.date_of_birth"
                                       v-validate="'required'" :state="validateState('date_of_birth')"
                                       data-vv-name="date_of_birth" :max="new Date()"
                    ></b-form-datepicker>
                    <label for="email" style="margin-bottom: 0px; font-size: 14px;">
                        Email:
                    </label>
                    <b-form-input id="email" v-model="user_data.email" size="sm" name="email"
                                  type="email" required v-validate="'required|email'"
                                  :state="validateState('email')" data-vv-name="email">
                    </b-form-input>
                    <label for="mobile_no" style="margin-bottom: 0px; font-size: 14px;">
                        Mobile No:
                    </label>
                    <b-form-input id="mobile_no" v-model="user_data.user_details.mobile_no" size="sm" name="mobile_no"
                                  type="number" required v-validate="'required|digits:10'"
                                  :state="validateState('mobile_no')" data-vv-name="mobile_no">
                    </b-form-input>
                    <span class="text-danger" style="font-size: 12px">
                        {{ veeErrors.first('mobile_no') }}
                    </span>
                    <div v-for="(i, index) in user_data.user_details.extra_phone" :key="index">
                        <label :for="`mobile_no ${index}`" style="margin-bottom: 0px; font-size: 14px;">
                            Mobile No {{index+1}}:
                        </label>
                        <b-input-group>
                            <b-form-input :id="`mobile_no ${index}`" v-model="i.mobile_no" size="sm"
                                          :name="`mobile_no ${index}`"
                                          type="number" required v-validate="'required|digits:10'"
                                          :state="validateState('mobile_no '+index)"
                                          :data-vv-name="`mobile_no ${index}`">
                            </b-form-input>
                            <template v-slot:append>
                                <b-button size="sm" variant="danger" @click="remove(index)">
                                    <b-icon icon="x" aria-label="delete"></b-icon>
                                </b-button>
                            </template>
                        </b-input-group>
                        <span class="text-danger" style="font-size: 12px">
                        {{ veeErrors.first('mobile_no '+index) }}
                    </span>
                    </div>
                    <b-row align="right" class="mt-3">
                        <b-col align-self="end">
                            <b-button variant="link" size="sm" @click="addMore()">
                                <b-icon icon="phone" aria-label="add More"></b-icon>
                                Add Another Phone no
                            </b-button>
                        </b-col>
                    </b-row>
                </b-card-text>
            </b-card>
            <b-card v-bind:key="10" v-if="current_step===2" align="left">
                <b-card-text>
                    <b-card-text>
                        <label for="username" style="margin-bottom: 0px; font-size: 14px;">
                            User Name:
                        </label>
                        <b-form-input id="username" v-model="user_data.username" size="sm" name="username"
                                      type="text" required v-validate="'required'"
                                      :state="validateState('username')" data-vv-name="username">
                        </b-form-input>
                        <label for="password" style="margin-bottom: 0px; font-size: 14px;">
                            Password:
                        </label>
                        <b-form-input id="password" v-model="user_data.password1" size="sm" name="password"
                                      type="password" required v-validate="'required'" ref="password"
                                      :state="validateState('password')" data-vv-name="password">
                        </b-form-input>

                        <label for="con_password" style="margin-bottom: 0px; font-size: 14px;">
                            Confirm Password:
                        </label>
                        <b-form-input id="confirm_password" v-model="user_data.password2"
                                      size="sm" name="confirm_password"
                                      type="password" required v-validate="'required|confirmed:password'"
                                      :state="validateState('confirm_password')" data-vv-name="confirm_password">
                        </b-form-input>
                        <span class="text-danger" style="font-size: 12px">
                            {{ veeErrors.first('confirm_password') }}
                        </span>
                    </b-card-text>
                </b-card-text>
            </b-card>
        </transition>
        <div class="mt-2">
            <b-row align-h="between">
                <b-col cols="4">
                    <b-button variant="light" v-if="current_step===2"
                              @click="transition_name='previous';current_step=1">Previous
                    </b-button>
                </b-col>
                <b-col cols="4" class="text-right">
                    <b-button variant="outline-primary" v-if="current_step===1"
                              :disabled="veeErrors.any()"
                              @click="next('next')">Next
                    </b-button>
                    <b-overlay
                            :show="loading"
                            rounded
                            opacity="0.6"
                            spinner-small
                            spinner-variant="primary"
                            class="d-inline-block"
                    >
                        <b-button variant="primary" v-if="current_step===2"
                                  @click="next('finesh')">Submit
                        </b-button>
                    </b-overlay>
                </b-col>
            </b-row>
        </div>
    </b-modal>
</template>

<script>
  export default {
    name: "add_user",
    props: ['modalShow',],
    data() {
      return {
        current_step: 1,
        transition_name: 'next',
        loading: false,
        visible: false,
        user_data: {
          first_name: null,
          last_name: null,
          user_details:{
            date_of_birth: null,
            mobile_no: null,
            extra_phone: [],},
          email: null,
          password1: null,
          password2: null,
        }
      }
    },
    watch: {
      modalShow() {
        this.visible = true;
        this.current_step = 1;
        this.user_data = {
          first_name: null,
          last_name: null,
          user_details:{
            date_of_birth: null,
            mobile_no: null,
            extra_phone: [],
          },
          email: null,
          password1: null,
          password2: null,
        }
      }
    },
    methods: {
      validateState(ref) {
        if (
          this.veeFields[ref] &&
          (this.veeFields[ref].dirty || this.veeFields[ref].validated)
        ) {
          return !this.veeErrors.has(ref);
        }
        return null;
      },
      addMore() {
        this.user_data.user_details.extra_phone.push(
          {
            mobile_no: null,
          })
      },
      remove(index) {
        this.user_data.user_details.extra_phone.splice(index, 1);
      },
      next(step){
        this.$validator.validateAll().then(isValid => {
          if (isValid) {
            if (step==='next') {
              this.transition_name = 'next';
              this.current_step = 2
            }
            if(step==='finesh'){
              this.axios.post('api/user/registration/', this.user_data).then(response => {
                if (response) {
                  this.$bvToast.toast('User Created successfully', {
                    title: 'Success',
                    variant: 'success',
                    autoHideDelay: 4000,
                  });
                  this.loading = false;
                  this.$emit('callList');
                  this.visible = false;
                }
              }).catch((error) => {
                let error_data=JSON.stringify(error.response.data);
                this.loading = false;
                this.$bvToast.toast(`${error_data}`, {
                  title: 'Failed',
                  variant: 'danger',
                  autoHideDelay: 10000,
                });
              });
            }
          }
        })
      }
    }
  }
</script>

<style>
    .next-enter-active, .next-leave-active,
    .previous-enter-active, .previous-leave-active {
        transition: all .3s ease;
    }

    .next-leave-to, .previous-enter
        /* .slide-fade-leave-active below version 2.1.8 */
    {
        transform: translateX(-10px);
        opacity: 0;
    }

    .next-enter, .previous-leave-to {
        transform: translateX(10px);
        opacity: 0;
    }
</style>