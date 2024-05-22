<script>
    import { goto } from '$app/navigation';
    import { onMount } from 'svelte';
    import { auth } from '$lib/firebase';
    import { EmailAuthProvider, reauthenticateWithCredential, updateEmail } from "firebase/auth";
  
    let currentPassword = '';
    let newEmail = '';
    let message = '';
  
    let user;
  
    onMount(() => {
      user = auth.currentUser;
      if (!user) {
        goto('./');
      }
    });
  
    async function handleChangeEmail() {
      if (!user) {
        message = 'No user is currently signed in.';
        return;
      }
  
      try {
        const credential = EmailAuthProvider.credential(user.email, currentPassword);
        await reauthenticateWithCredential(user, credential);
        await updateEmail(user, newEmail);
        message = 'Email updated successfully.';
      } catch (error) {
        console.error('Error:', error);
        message = `Error: ${error.message}`;
      }
    }
  </script>
  
  <form on:submit|preventDefault={handleChangeEmail}>
    <label>
      Current Password:
      <input type="password" bind:value={currentPassword} required />
    </label>
    <label>
      New Email:
      <input type="email" bind:value={newEmail} required />
    </label>
    <button type="submit">Update Email</button>
  </form>
  <p>{message}</p>
  