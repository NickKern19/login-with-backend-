<script>
	import Tailwindcss from './Tailwindcss.svelte';
    import Router from 'svelte-spa-router'
    import SignUp from './routes/SignUp.svelte'
    import SignIn from './routes/SignIn.svelte'
    import ForgotPassword from './routes/ForgotPassword.svelte'
    import {userbaseStore, userStore, promiseStore} from './stores'

	const userbase = window.userbase
    window.userbase = null

    // stores
    $userbaseStore = userbase
    $userStore = null

    $promiseStore = userbase.init({appId: '1443b981-7fac-459f-abd4-16c5149ef90e'})
        .then((session) => $userStore = session.user)

    
    function signout() {
        $promiseStore = $userbaseStore.signOut().then(() => $userStore = null)
    }
</script>

<Tailwindcss />

<div class="container flex flex-col justify-center items-center w-screen h-screen mx-auto">
    {#await $promiseStore.then(() => Promise.reject())}
        Loading..
    {:catch error}
        {#if error}
            <strong class="text-red-700 font-bold">ERROR! {error} </strong>
        {/if}
        {#if $userStore}
            Hello, {$userStore.username}!
            <button on:click={signout}>Logout</button>
        {:else}
            <Router routes={{
                '/': SignUp,
                '/signin': SignIn,
                '/forgotpassword': ForgotPassword
            }} />
        {/if}
    {/await}
</div>