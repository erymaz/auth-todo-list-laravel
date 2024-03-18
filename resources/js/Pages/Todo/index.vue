<script setup>
import { ref, computed } from 'vue';
import SecondaryButton from '@/Components/SecondaryButton.vue';
import DangerButton from '@/Components/DangerButton.vue';
import InputLabel from '@/Components/InputLabel.vue';
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head } from '@inertiajs/vue3';
import CreateTodoForm from './Partials/CreateTodoForm.vue';
import { useForm } from '@inertiajs/vue3';
import TextInput from '@/Components/TextInput.vue';
import Modal from '@/Components/Modal.vue';

const props = defineProps({
    errors: Object,
    todos: Object,
})

const confirmingUpdate = ref(false);
const selected = ref(null);

const confirmUpdate = (id) => {
    selected.value = id;
    confirmingUpdate.value = true;
};

const form = useForm({
    todo: ''
});

const deleteTodo = (id) => {
    form.delete(route('todos.destroy', { 'id': id }));
}

const updateTodo = () => {
    if (!selected.value) {
        return;
    }
    form.patch(route('todos.update', { 'id': selected.value }, form));
    closeModal();
}

const closeModal = () => {
    confirmingUpdate.value = false;
    form.reset();
};
</script>

<template>
    <Head title="Todo List" />
    <AuthenticatedLayout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">Todo List</h2>
        </template>
        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <CreateTodoForm />
                <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg p-8 mt-4 divide-y">
                    <div
                        v-for="todo of todos"
                        :key="todo.id"
                        class="flex justify-between items-center py-2 text-gray-900"
                    >
                        <div>
                            <span class="mr-10">{{ todo.todo }}</span>
                            <span v-if="todo.completed">Completed</span>
                        </div>
                        <div class="flex items-center space-x-4">
                            <span
                                class="text-green-500 font-semibold cursor-pointer"
                                @click="{
                                    form.todo = todo.todo;
                                    confirmUpdate(todo.id);
                                }"
                            >
                                Edit
                            </span>
                            <span
                                class="text-red-500 font-semibold cursor-pointer"
                                @click="deleteTodo(todo.id)"
                            >
                                Remove
                            </span>
                        </div>
                    </div>
                    <div v-if="!todos?.length">
                        No todos
                    </div>
                </div>
            </div>
        </div>
    </AuthenticatedLayout>
    <Modal :show="confirmingUpdate" @close="closeModal">
        <div class="p-6">
            <div class="mt-6">
                <InputLabel for="todo" value="Todo" />
                <TextInput
                    id="todo"
                    v-model="form.todo"
                    type="text"
                    class="mt-1 block w-full"
                    required
                />
            </div>
            <div class="mt-6 flex justify-end">
                <SecondaryButton @click="closeModal"> Cancel </SecondaryButton>
                <DangerButton
                    class="ms-3"
                    :class="{ 'opacity-25': form.processing }"
                    :disabled="form.processing"
                    @click="updateTodo"
                >
                    Save
                </DangerButton>
            </div>
        </div>
    </Modal>
</template>
