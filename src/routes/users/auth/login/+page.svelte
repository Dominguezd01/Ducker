<script>
    import { getCookie } from "../../../../lib/getCookie.js"
    import { setCookie } from "../../../../lib/setCookie.js"
    import { goto } from "$app/navigation"
    import { onMount } from "svelte"
    import ErrorDialog from "../../../../lib/Components/Dialogs/ErrorDialog.svelte"
    import { env } from "$env/dynamic/public"
    const API = env.PUBLIC_API
    let form,
        identifierValue,
        passwordValue,
        divDialog,
        submitButton,
        buttonContainer,
        login = true
    onMount(() => {
        if (getCookie("token")) {
            goto("/quacks/main")
        }
    })
    const handleSubmit = async () => {
        submitButton.addEventListener("click", preventClickWhenFetching)
        let body = {
            identifier: identifierValue,
            password: passwordValue,
        }
        let options = {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
            },
            body: JSON.stringify(body),
        }
        let response = await fetch(`${API}/users/auth/login`, options)
        response = await response.json()
        login = false
        if (response.status == 400) {
            new ErrorDialog({
                target: divDialog,
                props: {
                    title: "Error",
                    content: response.msg,
                },
            })
        } else if (response.status == 401) {
            new ErrorDialog({
                target: divDialog,
                props: {
                    title: "Error",
                    content: response.msg,
                },
            })
            login = true
        } else if (response.status == 200) {
            setCookie("token", response.token, 15)
            setCookie("userName", response.userName, 15)
            setCookie("userDisplayName", response.userDisplayName, 15)
            localStorage.setItem("userImage", response.profileImage)
            //redirect to main page
            goto("/quacks/main")
        }

        submitButton.removeEventListener(
            "click",
            preventClickWhenFetching,
            false,
        )
    }

    const preventClickWhenFetching = (e) => {
        e.preventDefault()
    }
</script>

<div bind:this={divDialog}></div>
<div class="grid items-center justify-center m-14 login">
    <form
        on:submit|preventDefault={handleSubmit}
        class="flex flex-col items-center justify-center gap-2"
    >
        <div
            class="grid items-center grid-cols-2 gap-6 text-center inputContainer"
        >
            <div class="grid items-center">
                <label for="email">Username or Email</label>
                <input
                    required
                    type="text"
                    id="userName"
                    bind:value={identifierValue}
                    class="w-72 text-center rounded-lg h-10 hover:border-solid hover:border-2 border-quacker transition 0.25s"
                />
            </div>
            <div class="grid items-center">
                <label for="email">Password</label>
                <input
                    required
                    type="password"
                    id="password"
                    bind:value={passwordValue}
                    class="w-72 text-center rounded-lg h-10 hover:border-solid hover:border-2 border-quacker transition 0.25s"
                />
            </div>
        </div>
        <a href="/users/auth/register" class="text-green-500 underline"
            >Don't have an account? Register</a
        >
        <div bind:this={buttonContainer}>
            <input
                bind:this={submitButton}
                type="submit"
                class="bg-green-600 text-center w-36 h-12 rounded-lg hover:bg-green-400 transition 0.35s"
                Login
            />
        </div>
    </form>
</div>

<style>
    @media (min-width: 300px) and (max-width: 1900px) {
        .inputContainer {
            display: flex;
            flex-direction: column;
        }
    }
</style>
