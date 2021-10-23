<template>
    <div class="form">
        <h1 class="form__title">Sign up</h1>

        <ValidationObserver ref="signUpForm">

            <div class="row m-b-26">
                <SimpleField
                    label="First name"
                    classComponent="col-6"
                    rules="required"
                    @change-value="formData.firstName = $event"
                ></SimpleField>

                <SimpleField
                    label="Last name"
                    classComponent="col-6"
                    rules="required"
                    @change-value="formData.lastName = $event"
                ></SimpleField>
            </div>

            <div class="row m-b-26 align-items-center">
                <SimpleField
                    label="Email"
                    classComponent="col-6"
                    rules="required|email"
                    @change-value="formData.email = $event"
                ></SimpleField>

                <div class="col-6">
                    <div class="checkbox">
                        <input type="checkbox" id="plainText" name="plainText" v-model="formData.sendEmail">
                        <label for="plainText" class="checkbox__label">Send me email in plain text</label>
                    </div>
                </div>
            </div>

            <div class="row m-b-26 ">
                <div class="col-12">
                    <DoubleField
                        firstLabel="Personal code number"
                        secondLabel="Personal phone number"
                        rules="required"
                        @change-code="changeCode('personal', $event)"
                        @change-phone="changePhone('personal', $event)"
                    />
                </div>
            </div>

            <div class="row m-b-26 ">
                <SimpleField
                    label="Company name"
                    classComponent="col-12"
                    rules="required"
                    @change-value="formData.companyName = $event"
                ></SimpleField>
            </div>

            <div class="row m-b-26">
                <ValidationProvider
                    class="col-12"
                    v-slot="{ errors }"
                    name="Company address"
                    rules="required"
                >
                    <FormGroup :error="errors[0]" label="Company address">
                        <input type="text" v-model="formData.companyAddress"
                               @keyup="loadPlace(formData.companyAddress)"
                               @keydown="clearTypingTimer">
                    </FormGroup>
                    <span ref="test"></span>
                </ValidationProvider>
            </div>

            <div class="row m-b-26 ">
                <div class="col-12">
                    <DoubleField
                        firstLabel="Company code number"
                        secondLabel="Company phone number"
                        rules="required"
                        @change-code="changeCode('company', $event)"
                        @change-phone="changePhone('company', $event)"
                    />
                </div>
            </div>

            <div class="row m-b-26">
                <SimpleField
                    label="Job title"
                    classComponent="col-12"
                    rules="required"
                    @change-value="formData.jobTitle = $event"
                ></SimpleField>
            </div>

            <div class="row m-b-26 ">
                <ValidationProvider
                    class="col-6"
                    v-slot="{ errors }"
                    name="Password"
                    rules="required|min:10"
                >
                    <FormGroup :error="errors[0]" class="password" label="Password" :class="classPassword">
                        <input :type="fieldPasswordType" v-model="formData.password" @input="checkPassword()">
                        <button @click="showPassword()" class="btn-show-password">
                            <i class="fa" :class="fieldPasswordType === 'password' ? 'fa-eye' : 'fa fa-eye-slash'"></i>
                        </button>
                    </FormGroup>
                </ValidationProvider>

                <SimpleField
                    label="Confirm password"
                    classComponent="col-6"
                    rules="required|confirmed:Password"
                    @change-value="formData.confirmPassword = $event"
                ></SimpleField>
            </div>

            <div class="row m-b-26">
                <div class="col-6">
                    <FormGroup :error="errorAccept ? 'The Accept field is required' : ''">
                        <div class="checkbox">
                            <input type="checkbox" id="accept" name="accept" v-model="formData.accept"
                                   @change="errorAccept = false">
                            <label for="accept" class="checkbox__label">Accept
                                <a href="#" class="link">Privacy Policy</a>
                            </label>
                        </div>
                    </FormGroup>
                </div>
            </div>

            <div class="form__footer">
                <button class="btn" @click="submitForm()">Sign up</button>
            </div>
        </ValidationObserver>
    </div>

</template>

<script>
import FormGroup from './components/FormGroup.vue'
import SimpleField from './components/SimpleField.vue'
import DoubleField from './components/DoubleField.vue'
import axios from 'axios'

export default {
    name: 'App',

    components: {
        FormGroup,
        SimpleField,
        DoubleField
    },

    data () {
        return {
            apiKey: 'AIzaSyABCnfrUho1FpFy0unBOIQGjzxsb5CcgPk',
            errorAccept: false,
            fieldPasswordType: 'password',
            formData: {
                firstName: '',
                lastName: '',
                email: '',
                personalCode: '',
                personalPhone: '',
                companyName: '',
                companyCode: '',
                companyPhone: '',
                companyAddress: '',
                jobTitle: '',
                password: null,
                confirmPassword: '',
                sendEmail: false,
                accept: false
            },
            passwordWeakness: {
                passwordLength: null,
                lowercase: false,
                uppercase: false,
                specialCharacter: false
            },
            typingTimer: null
        }
    },

    computed: {
        classPassword () {
            if (!this.formData.password) return
            const passwordWeaknessConditions = Object.values(this.passwordWeakness).filter((item) => item === true).length
            if (this.passwordWeakness.passwordLength < 10) return 'weak-password'
            return {
                'weak-password': passwordWeaknessConditions <= 1,
                'medium-password': passwordWeaknessConditions === 2,
                'strong-password': passwordWeaknessConditions === 3
            }
        }
    },

    methods: {
        clearTypingTimer () {
            clearTimeout(this.typingTimer)
        },

        loadPlace (value) {
            if (!value) return

            clearTimeout(this.typingTimer)
            this.typingTimer = setTimeout(() => {
                const url = `https://maps.googleapis.com/maps/api/place/findplacefromtext/json?fields=name&input=${value}&inputtype=textquery&key=${this.apiKey}`
                const config = {
                    method: 'get',
                    url,
                    headers: {}
                }

                axios(config)
                    .then(function (response) {
                        console.log(JSON.stringify(response.data))
                    })
                    .catch(function (error) {
                        console.log(error)
                    })
            }, 1000)
        },

        showPassword () {
            this.fieldPasswordType = this.fieldPasswordType === 'password' ? 'text' : 'password'
        },

        checkPassword () {
            const format = /[ !@#$%^&*()_+\-=\[\]{};':"\\|,.<>\/?]/
            this.passwordWeakness.passwordLength = this.formData.password.length
            this.passwordWeakness.lowercase = /[a-z]/.test(this.formData.password)
            this.passwordWeakness.uppercase = /[A-Z]/.test(this.formData.password)
            this.passwordWeakness.specialCharacter = format.test(this.formData.password)
        },

        async submitForm () {
            const isValid = await this.$refs.signUpForm.validate()
            this.errorAccept = !this.formData.accept
            if (!isValid || this.errorAccept) return

            console.log(this.formData)
        },

        changeCode (key, {
            code,
            phone
        }) {
            this.formData[`${key}Phone`] = phone
            this.formData[`${key}Code`] = code
        },

        changePhone (key, value) {
            this.formData[`${key}Phone`] = value
        }
    }
}
</script>

<style lang="scss">
:root {
    --main-text-color: #333333;
    --error-text-color: #FC0202;
    --link-text-color: #5B62FF;
    --icon-color: #979797;
    --border-color: #D8D8D8;
    --btn-bg-color: #E9C864;
}

*, *:before, *:after {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    padding: 20px;
    font-family: 'Lato', sans-serif;
    background: #000;
    box-sizing: border-box;
    color: var(--main-text-color);

}

:focus {
    outline: none;
}

input[type="text"],
input[type="password"],
input[type="email"],
input[type="number"] {
    padding: 0 10px;
    width: 100%;
    height: 40px;
    border: 1px solid var(--border-color);
    border-radius: 2px;
    font-size: 12px;
    line-height: 13px;
}

select {
    border: 1px solid var(--border-color);
    border-radius: 2px;
    font-size: 12px;
    height: 40px;
    width: 100%;
}

.row {
    display: flex;
    margin: 0 -15px;
}

[class*="col-"] {
    padding: 0 15px;
}

.col-6 {
    flex: 0 0 50%;
    max-width: 50%;
}

.col-12 {
    flex: 0 0 100%;
    max-width: 100%;
}

.m-b-26 {
    margin-bottom: 26px;
}

.align-items-center {
    align-items: center;
}

.link {
    text-transform: uppercase;
    color: var(--link-text-color);
    font-size: 11px;
}

.checkbox {
    display: flex;

    &__label {
        cursor: pointer;
        padding-left: 8px;
        text-transform: uppercase;
        font-weight: bold;
        font-size: 11px;
    }
}

.btn {
    background: var(--btn-bg-color);
    border: none;
    border-radius: 5px;
    padding: 11px 45px;
    font-size: 16px;
    min-width: 270px;
    cursor: pointer;

    &:hover {
        opacity: .9;
    }
}

.btn-show-password {
    border: none;
    background: none;
    color: var(--border-color);
    font-size: 18px;
}

.double-fields {
    display: flex;

    & > :first-child {
        width: 50%;

        select {
            border-radius: 2px 0 0 2px;
            border-right: 0;
        }
    }

    & > :last-child {
        width: 50%;

        select {
            border-radius: 0 2px 2px 0;
        }
    }
}

.form {
    background: #fff;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
    margin: 0 auto;
    width: 671px;
    padding: 45px 48px 50px 54px;

    &__title {
        font-size: 24px;
        margin: 0 0 42px;
        padding: 0;
        text-align: center;
    }

    &__footer {
        text-align: right;
        padding-top: 24px;
        border-top: 1px solid var(--border-color)
    }
}
</style>
