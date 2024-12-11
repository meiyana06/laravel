<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import Chirp from '@/Components/Chirp.vue';
import InputError from '@/Components/InputError.vue';
import PrimaryButton from '@/Components/PrimaryButton.vue';
import { useForm, Head } from '@inertiajs/vue3';
import { ref } from 'vue';
import Editor from '@tinymce/tinymce-vue';


defineProps(['chirps']);

const form = useForm({
    message: '',
    media: null,
});

const mediaPreview = ref(null); // For showing a preview of the uploaded media

// Handle file input change
const handleFileChange = (event) => {
    const file = event.target.files[0];
    form.media = file;
    
    // Create a preview of the uploaded file
    if (file) {
        const reader = new FileReader();
        reader.onloadend = () => {
            mediaPreview.value = reader.result;
        };
        reader.readAsDataURL(file);
    }
};

// Function to submit form with media
const submitForm = () => {
    const formData = new FormData();
    formData.append('message', form.message);
    
    if (form.media) {
        formData.append('media', form.media);
    }

    form.post(route('chirps.store'), {
        data: formData,
        onSuccess: () => form.reset(),
    });
};
</script>

<template>
    <Head title="Chirps" />

    <AuthenticatedLayout>
        <div class="max-w-2xl mx-auto p-4 sm:p-6 lg:p-8">
            <form @submit.prevent="submitForm">
                <Editor
                    api-key="indbfowaszd8agjp2bkn7okje1k70164e0m0yl1wt4erimsc"
                    :init="{
                        toolbar_mode: 'sliding',
                        plugins: [
                            'anchor', 'autolink', 'charmap', 'codesample', 'emoticons', 'image', 'link', 'lists', 'media', 'searchreplace', 'table', 'visualblocks', 'wordcount',
                            'checklist', 'mediaembed', 'casechange', 'export', 'formatpainter', 'pageembed', 'a11ychecker', 'tinymcespellchecker', 'permanentpen', 'powerpaste', 'advtable', 'advcode', 'editimage', 'advtemplate', 'ai', 'mentions', 'tinycomments', 'tableofcontents', 'footnotes', 'mergetags', 'autocorrect', 'typography', 'inlinecss', 'markdown',
                            'importword', 'exportword', 'exportpdf'
                        ],
                        toolbar: 'undo redo | blocks fontfamily fontsize | bold italic underline strikethrough | link image media table mergetags | addcomment showcomments | spellcheckdialog a11ycheck typography | align lineheight | checklist numlist bullist indent outdent | emoticons charmap | removeformat',
                        tinycomments_mode: 'embedded',
                        tinycomments_author: 'Author name',
                        mergetags_list: [
                            { value: 'First.Name', title: 'First Name' },
                            { value: 'Email', title: 'Email' },
                        ],
                    }"
                    v-model="form.message"
                />

                <!-- File input for image or video -->
                <div class="mt-2">
                    <input 
                        type="file" 
                        accept="image/jpg,video/mp4"
                        @change="handleFileChange"
                        class="block w-full border-gray-300 focus:border-indigo-300 focus:ring focus:ring-indigo-200 focus:ring-opacity-50 rounded-md shadow-sm"
                    />
                    <InputError :message="form.errors.media" class="mt-2" />
                </div>
                
                <!-- Preview media (if any) -->
                <div v-if="mediaPreview" class="mt-4">
                    <div v-if="form.media.type.startsWith('image')">
                        <img :src="mediaPreview" alt="Uploaded Preview" class="max-w-full h-auto rounded-md"/>
                    </div>
                    <div v-if="form.media.type.startsWith('video')">
                        <video :src="mediaPreview" controls class="max-w-full h-auto rounded-md"></video>
                    </div>
                </div>

                <InputError :message="form.errors.message" class="mt-2" />
                <PrimaryButton class="mt-4">Chirp</PrimaryButton>                
            </form>
            <div class="mt-6 bg-white shadow-sm rounded-lg divide-y">
                <Chirp
                    v-for="chirp in chirps"
                    :key="chirp.id"
                    :chirp="chirp"
                />
            </div>
        </div>
    </AuthenticatedLayout>
</template>
