<script setup>
import InputError from '@/Components/InputError.vue';
import InputLabel from '@/Components/InputLabel.vue';
import PrimaryButton from '@/Components/PrimaryButton.vue';
import TextInput from '@/Components/TextInput.vue';
import { useForm } from '@inertiajs/vue3';

const form = useForm({
    todo: '',
});

const props = defineProps({
    errors: Object
});

const submit = async() => {
    try {
        form.post(route('todos.store'), form);
        form.reset();
    } catch (err) {
        form.reset();
    }
}
</script>

<template>
    <section>
        <form @submit.prevent="submit" class="flex items-end mt-6 space-x-2">
            <div class="flex-1">
                <InputLabel for="todo" value="Todo" />

                <TextInput
                    id="todo"
                    type="text"
                    class="mt-1 block w-full h-9"
                    v-model="form.todo"
                    required
                    autofocus
                />

                <InputError class="mt-2" :message="form.errors.todo" />
            </div>

            <div class="flex items-center">
                <PrimaryButton :disabled="form.processing">Save</PrimaryButton>
            </div>
        </form>
    </section>
</template>
