<template>
    <Modal @close="closeModal" :show="show" :max-width="'md'">
        <form class="flex flex-col w-full" @submit.prevent="submit">
            <div class="flex items-center justify-between w-full">
                <BankModalIcon class="flex-shrink-0"/>
                
                <button type="button" @click="closeModal">
                    <ModalCloseIcon/>
                </button>
            </div>

            <span class="font-sans text-lg font-semibold text-[#F5F5F6] mt-4">Add Institution</span>
            <span class="font-sans text-sm text-[#94969C]">Add a new institution where you bank at.</span>

            <div class="flex flex-col mt-5">
                <InputLabel value="Name"/>
                <TextInput v-model="form.name" class="mt-1"/>
                <InputError class="mt-2" :message="form.errors.name" />
            </div>

            <div class="flex flex-col mt-5">
                <InputLabel value="URL"/>
                <TextInput v-model="form.url" class="mt-1"/>
                <InputError class="mt-2" :message="form.errors.url" />
            </div>

            <div class="pt-8 flex items-center justify-end">
                <button type="button" @click="closeModal" class="bg-[#161B26] text-[#CECFD2] cursor-pointer px-4 py-[10px] rounded-lg font-semibold border border-[#333741]">
                    Cancel
                </button>
                <button class="ml-3 bg-[#155EEF] text-white cursor-pointer px-4 py-[10px] rounded-lg font-semibold border border-[#155EEF]">
                    Add
                </button>
            </div>
        </form>
        
    </Modal>
</template>

<script setup>
import Modal from '@/Components/Modal.vue';
import BankModalIcon from '@/Components/Icons/BankModalIcon.vue';
import ModalCloseIcon from '@/Components/Icons/ModalCloseIcon.vue';
import InputError from '@/Components/InputError.vue';
import InputLabel from '@/Components/InputLabel.vue';
import TextInput from '@/Components/TextInput.vue';
import { ref } from 'vue';
import { useForm } from '@inertiajs/vue3';
import { useEventBus } from '@vueuse/core'

const promptBus = useEventBus('ff-prompt-event-bus')
const notifyBus = useEventBus('ff-notification-event-bus')
const show = ref(false);

const form = useForm({
    name: '',
    url: '',
});

const listener = ( event ) => {
    if( event == 'prompt-add-institution' ){
        show.value = true;
    }
}
promptBus.on(listener);

const closeModal = () => {
    form.clearErrors();
    form.reset();
    show.value = false;
}

const submit = () => {
    form.post(`/settings/institutions`, {
        preserveScroll: true,
        onSuccess: () => {
            show.value = false;
            form.reset();
            notifyBus.emit('notify', {
                title: 'Institution Added',
                body: 'Institution has been added and is ready for use.',
                type: 'success'
            });
        }
    })
}
</script>