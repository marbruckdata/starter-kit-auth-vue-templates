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

## Setup routes in web.php
```php
if (Features::enabled(Features::registration())) {
    Route::get('/auth/register', RegisterIndexController::class)->name('auth.register');
}

Route::get('/auth/login', LoginIndexController::class)->name('auth.login');
```



## Populate current signed in User

- Create a UserResource with php artisan make:resource UserResource
```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class UserResource extends JsonResource
{
    /**
     * Transform the resource into an array.
     *
     * @return array<string, mixed>
     */
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
        ];
    }
}
```


- In app/Http/Middleware/HandleInertiaRequests.php there should be the auth attribute:

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

- In any template use {{ $page.props.auth.user }} to get information of the current signed in user
- User name: {{ $page.props.auth.user.name }}
- User email: {{ $page.props.auth.user.email }}


- Get rid of the data wrapper from like data: { ... } In app/Providers/AppServiceProvider.php use in boot function 'JsonResource::withoutWrapping();'
```php
<?php

namespace App\Providers;

use Illuminate\Http\Resources\Json\JsonResource;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     */
    public function register(): void
    {
        //
    }

    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        JsonResource::withoutWrapping();
    }
}
```

## Upgrade to register, login form (and any other form)
- Create in Components directory an InputField.vue
```javascript
<script>
export default {
    name: "InputField",
    props: {
        label: {
            type: String,
            required: false,
        },
        id: {
            type: String,
            required: true,
        },
        placeholder: {
            type: String,
            required: true,
        },
        type: {
            type: String,
            default: "text",
        },
        modelValue: {
            type: String,
            required: true,
        },
        errors: {
            type: String,
            default: null,
        },
    },

};
</script>

<template>
    <div>
        <label :for="id" class="text-sm font-medium text-gray-900">{{ label }}</label>
        <div class="mt-2">
            <input
                :type="type"
                :id="id"
                :placeholder="placeholder"
                :class="'w-full py-2 text-gray-900 border-gray-300 text-sm ' + (errors ? 'border-red-500' : '')"
                :value="modelValue"
                @input="$emit('update:modelValue', $event.target.value)"
            />
            <div v-if="errors" class="text-sm text-red-500 mt-2">
                {{ errors }}
            </div>
        </div>
    </div>
</template>

<style scoped>
</style>

```

Instead of using this:
```javascript
<div>
    <label for="name" class="">Name</label> // label for
    <div class="mt-2">
        <input type="text" id="name" class="" v-model="form.name"> // input type, input id, input v-model
        <div v-if="form.errors.name" class="text-sm text-red-500 mt-2"> // on error
            {{ form.errors.name }} // on error label
        </div>
    </div>
</div>
```

We use this (example for email, aplly any other fields see above):
```javascript
<script setup>
import InputField from "@/Components/InputField.vue";
</script>

<template>
<InputField
    id="email"
    v-model="form.email"
    placeholder="Enter Email"
    :errors="form.errors.email"
/>
</template>
```

## Logout User
```javascript
<script setup>
import { Link, router } from '@inertiajs/vue3'

const logout = () => {
    router.post(route('logout'))
}
</script>

<template>

    // VERSION 1
    <button v-on:click="logout" class="block px-4 py-2 text-sm text-gray-900 w-full text-left" :class="{ 'bg-gray-100': active }">
        Sign out
    </button>

    // VERSION 2
    <Link href="/logout" method="post" as="button" class="text-blue-500 hover:underline">Log Out</Link>              
</template>
```
