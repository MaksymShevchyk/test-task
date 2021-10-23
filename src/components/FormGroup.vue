<template>
    <div class="form-group">
        <label class="form-group__label">
            {{ label }}
        </label>

        <slot></slot>

        <small v-show="error" class="form-group__error">{{ error }}</small>
    </div>
</template>

<script>
export default {
    name: 'FormGroup',

    props: {
        label: {
            type: String,
            default: ''
        },

        error: {
            type: String,
            default: ''
        }
    }
}
</script>
<style lang="scss">
.form-group {
    position: relative;

    &__label {
        min-height: 13px;
        color: var(--main-text-color);
        font-size: 11px;
        font-weight: bold;
        text-transform: uppercase;
        display: block;
        margin-bottom: 8px;
    }

    &__error {
        position: absolute;
        bottom: -16px;
        display: block;
        color: var(--error-text-color);
        font-size: 11px;
    }

    &.password {
        position: relative;

        .btn-show-password {
            top: 50%;
            right: 10px;
            position: absolute;
        }

        &:before {
            position: absolute;
            top: 0;
            right: 0;
            font-size: 11px;
            text-transform: uppercase;
        }

        input {
            padding-right: 35px;
        }
    }

    &.weak-password {
        position: relative;

        &:before {
            content: 'Weak';
            color: red;
        }

        input {
            border-bottom: 3px solid red;
        }
    }

    &.medium-password {
        position: relative;

        &:before {
            content: 'Medium';
            color: orange;
        }

        input {
            border-bottom: 3px solid orange;
        }
    }

    &.strong-password {
        position: relative;

        &:before {
            content: 'Strong';
            color: green;
        }

        input {
            border-bottom: 3px solid green;

        }
    }
}
</style>
