<template>
  <div>
    <div>
      <h1 class="tracking-tight text-3xl font-semibold mb-2">{{ title }}</h1>
      <p class="text-muted-foreground">{{ description }}</p>
    </div>
    <UiDivider class="my-8" />
    <form @submit="submit">
      <fieldset class="grid grid-cols-1 gap-5 max-w-sm">
        <UiVeeInput name="firstName" label="First Name" required />
        <UiVeeInput name="lastName" label="Last Name" required />
        <UiVeeInput name="email" label="Email" type="email" required />
        <UiVeeInput name="phone" label="Phone Number" type="tel" required />
        <UiVeeFileInput name="image" label="Image" accept="image/*" required />
        <UiButton type="submit">Create Contact</UiButton>
      </fieldset>
    </form>
  </div>
</template>

<script lang="ts" setup>
import { faker } from "@faker-js/faker";
definePageMeta({
  layout: "admin",
});
const title = "Create Contact";
const description = "Create a contact";
useSeoMeta({ title: title, description: description });

const { handleSubmit, setValues } = useForm({
  validationSchema: toTypedSchema(ContactSchema),
});
const submit = handleSubmit(async (values) => {
  try {
    const res = await useContactStore().create({
      firstName: values.firstName,
      lastName: values.lastName,
      email: values.email,
      phone: values.phone,
    });
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
    useSonner.success("Contact created successfully");
    await navigateTo(`/admin/contacts`);
  } catch (error) {
    useSonner.error("Failed to create contact");
  }
});

onMounted(() => {
  if (import.meta.dev) {
    setValues({
      firstName: faker.person.firstName(),
      lastName: faker.person.lastName(),
      email: faker.internet.email(),
      phone: faker.phone.number(),
    });
  }
});
</script>

<style></style>
