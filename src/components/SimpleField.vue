<template>
    <ValidationProvider
        :class="classComponent"
        v-slot="{ errors }"
        :name="label"
        :rules="rules"
    >
        <FormGroup :error="errors[0]" :label="label">
            <input type="text" v-model="value" @change="$emit('change-value', value)">
        </FormGroup>
    </ValidationProvider>
</template>

<script>
import FormGroup from './FormGroup.vue'

export default {
    name: 'SimpleField',

    components: {
        FormGroup
    },

    props: {
        label: {
            type: String,
            default: ''
        },

        classComponent: {
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
            value: ''
        }
    }
}
</script>

<style scoped>

</style>
