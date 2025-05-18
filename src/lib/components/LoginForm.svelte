<!-- src/routes/+page@.svelte -->

<script lang="ts">
  import { Label, Input } from 'flowbite-svelte';
  import { SignIn } from 'flowbite-svelte-admin-dashboard';
  import monImage from '$lib/images/cssm.png';



  let title = 'Connection';
  let site = {
    name: '',
    img: monImage,
    link: '/',
    imgAlt: 'Logo-CSSM'
  };
  let rememberMe = false;
  let lostPassword = false;
  let createAccount = false;
  let lostPasswordLink = '/';
  let loginTitle = "Connexion à l'application";
  let registerLink = '/';
  let createAccountTitle = "Création d'identifiant";

  const onSubmit = (e: Event) => {
    const formData = new FormData(e.target as HTMLFormElement);

    const data: Record<string, string | File> = {};
    for (const field of formData.entries()) {
      const [key, value] = field;
      data[key] = value;
    }
    console.log(data);
  };
</script>

<SignIn {title} {site} {rememberMe} {createAccount} {loginTitle} {registerLink} onsubmit={onSubmit}>
  <div>
    <Label for="email" class="mb-2 dark:text-white">Identifiant</Label>
    <Input type="email" name="email" id="email" placeholder="nom.prenom@cssm.fr" required class="border outline-none dark:border-gray-600 dark:bg-gray-700" />
  </div>
  <div>
    <Label for="password" class="mb-2 dark:text-white">Mots de passe</Label>
    <Input type="password" name="password" id="password" placeholder="••••••••" required class="border outline-none dark:border-gray-600 dark:bg-gray-700" />
  </div>
</SignIn>

