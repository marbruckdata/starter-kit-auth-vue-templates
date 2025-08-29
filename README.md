## starter-kit-auth-vue-templates

## Register Form
- label for, input id: name, email, password, password_confirmation
- label itself e.g. : Name, Email, Password, Confirm password
- input type: text, text, password, password
- on error: v-if="form.errors.name", v-if="form.errors.email", v-if="form.errors.password", v-if="form.errors.password_confirmation"
- on error label: {{ form.errors.name }}, {{ form.errors.email }}, {{ form.errors.password }}, {{ form.errors.password_confirmation }}
- input v-model: form.name, form.email, form.password, form.password_confirmation

## Login Form
- label for, input id: email, password
- label itself e.g. : Email, Password
- input type: text, password
- on error: v-if="form.errors.email", v-if="form.errors.password"
- on error label: {{ form.errors.email }}, {{ form.errors.password }}
- input v-model: form.email, form.password


```javascript
<script setup>
import Modal from '@/Components/Modal.vue'
import { useForm, Head } from '@inertiajs/vue3'

const form = useForm({
    name: '',
    email: '',
    password: '',
    password_confirmation: ''
})
</script>

<template>
    <Modal class="">
        <h2 class="">Create an account</h2>
        <form class="" v-on:submit.prevent="form.post(`/register`)">
            <div>
                <label for="name" class="">Name</label> // label for
                <div class="mt-2">
                    <input type="text" id="name" class="" v-model="form.name"> // input type, input id, input v-model
                    <div v-if="form.errors.name" class="text-sm text-red-500 mt-2"> // on error
                        {{ form.errors.name }} // on error label
                    </div>
                </div>
            </div>
            <div>
                <button type="submit" class="disabled:opacity-50" :disabled="form.processing">
                    Create account
                </button>
            </div>
        </form>
    </Modal>
    <Head title="Create an account" />
</template>
```

## Populate current signed in User

In app/Http/Middleware/HandleInertiaRequests.php there should be the auth attribute:

```php
public function share(Request $request): array
    {
        return array_merge(parent::share($request), [
            'auth' => [
                'user' => $request->user() ? UserResource::make($request->user()) : null
            ],
        ]);
    }
```
