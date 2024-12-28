<template>
  <div>
    <div>
      <h1 class="tracking-tight text-3xl font-semibold mb-2">{{ title }}</h1>
      <p class="text-muted-foreground">{{ description }}</p>
    </div>
    <UiDivider class="my-8" />
    <div v-if="contact?.data.image && contact.data.image.url">
      <UiAvatar class="size-20" :src="contact.data.image.url" />
    </div>
    <div v-else>
      <UiAvatar class="size-20">
        <UiAvatarFallback>
          <UiIcon class="size-8" icon="lucide:user" />
        </UiAvatarFallback>
      </UiAvatar>
    </div>
    <form @submit="submit" class="mt-8s">
      <fieldset class="grid grid-cols-1 gap-5 max-w-sm">
        <UiVeeInput name="firstName" label="First Name" required />
        <UiVeeInput name="lastName" label="Last Name" required />
        <UiVeeInput name="email" label="Email" type="email" required />
        <UiVeeInput name="phone" label="Phone Number" type="tel" required />
        <UiVeeFileInput
          name="image"
          label="New Image"
          accept="image/*"
          ref="imageInput"
        />
        <UiButton type="submit">Update Contact</UiButton>
      </fieldset>
    </form>
  </div>
</template>

<script lang="ts" setup>
definePageMeta({
  layout: "admin",
});
const title = "Edit Contact";
const description = "Edit this contact";

const route = useRoute("admin-contacts-edit-id");

const { data: contact, refresh } = useAsyncData(() =>
  useContactStore().getById(route.params.id)
);
useSeoMeta({ title: title, description: description });

const imageInput = ref<HTMLInputElement | null>(null);
const { handleSubmit, setValues } = useForm({
  validationSchema: toTypedSchema(UpdateContactSchema),
  initialValues: contact.value?.data,
});
const submit = handleSubmit(async (values) => {
  try {
    console.log(values);
    const res = await useContactStore().update(route.params.id, {
      firstName: values.firstName,
      lastName: values.lastName,
      email: values.email,
      phone: values.phone,
    });
    if (values.image) {
      if (values.image instanceof File) {
        if (contact.value?.data.image) {
          await useStrapiClient()(`upload/files/${contact.value.data.image.id}`, {
            method: "DELETE",
          });
        }
        const formData = new FormData();
        formData.append("files", values.image);
        formData.append("refId", `${res.data.id}`);
        formData.append("ref", "api::contact.contact");
        formData.append("field", "image");
        const client = useStrapiClient();
        await client("upload", {
          method: "POST",
          body: formData,
        });
      }
    }

    useSonner.success("Contact edited successfully");
    await refresh();
    imageInput.value!.value = "";
    setValues(contact.value?.data || {});
  } catch (error) {
    useSonner.error("Failed this edit contact");
  }
});
</script>

<style></style>
