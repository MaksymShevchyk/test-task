<template>
    <div class="double-fields">
        <ValidationProvider
            v-slot="{ errors }"
            :name="firstLabel"
            :rules="rules"
        >
            <FormGroup :error="errors[0]" :label="secondLabel">
                <select v-model="code" @change="changeSelect()">
                    <option v-for="code in phoneCodes" :key="code.id" :value="code.id">
                        {{ code.title }}
                        ({{ code.countryCode }})
                    </option>
                </select>
            </FormGroup>
        </ValidationProvider>
        <ValidationProvider
            v-slot="{ errors }"
            :name="secondLabel"
            :rules="rules"
        >
            <FormGroup :error="errors[0]">
                <input type="text" :placeholder="getMask" v-mask="getMask" v-model="phone" @change="$emit('change-phone', phone)">
            </FormGroup>
        </ValidationProvider>
    </div>
</template>

<script>
import { mask } from 'vue-the-mask'
import FormGroup from './FormGroup.vue'

export default {
    name: 'DoubleField',

    directives: { mask },

    components: {
        FormGroup
    },

    props: {
        firstLabel: {
            type: String,
            default: ''
        },

        secondLabel: {
            type: String,
            default: ''
        },

        rules: {
            type: String,
            default: ''
        }
    },

    data () {
        return {
            phone: '',
            code: '',
            phoneCodes: [
                {
                    id: 0,
                    title: 'Afghanistan',
                    countryCode: '+93',
                    mask: '##-###-##-##'

                },
                {
                    id: 1,
                    title: 'Ukraine',
                    countryCode: '+380',
                    mask: '#-#-#-#-#-#-#-#-#'

                },
                {
                    id: 2,
                    title: 'USA Area Codes',
                    countryCode: '+1',
                    mask: '###-###-###'
                }
            ]
        }
    },

    computed: {
        getMask () {
            if (this.code === '') return ''
            return this.phoneCodes.find(item => item.id === this.code).mask
        }
    },

    methods: {
        changeSelect () {
            this.phone = ''
            this.$emit('change-code', {
                code: this.code,
                phone: this.phone
            })
        }
    }
}
</script>
