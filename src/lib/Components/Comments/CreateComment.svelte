<script>
    import { getCookie } from "../../getCookie"
    import Comment from "./Comment.svelte"
    import { env } from "$env/dynamic/public"
    import Swal from "sweetalert2"
    const API = env.PUBLIC_API

    export let quackId
    let textArea, content
    const handleSubmit = async () => {
        let content = textArea.value.trim()
        textArea.value = ""
        if (content === "" || content.length > 135) {
            Swal.fire({
                icon: "error",
                title: "Content too long",
                showCloseButton: true,
            })
            return
        }

        let bodyContent = {
            content: content,
            quackId: quackId,
        }
        let options = {
            method: "POST",
            headers: {
                "Content-Type": "application/json",
                authorization: getCookie("token").trim(),
            },
            body: JSON.stringify(bodyContent),
        }

        let response = await fetch(`${API}/comments/comment/create`, options)
        response = await response.json()

        if (response.status === 201) {
            new Comment({
                target: document.querySelector("#commentsContainer"),
                anchor: document.querySelector("#commentsContainer").firstChild,
                props: {
                    commentInfo: response.comment,
                },
                hydrate: false,
            })
        }
        if (response.status === 401 || response.status == 403) {
            let cookies = document.cookie.split(";")

            for (let i = 0; i < cookies.length; i++) {
                let cookie = cookies[i]
                let eqPos = cookie.indexOf("=")
                let name = eqPos > -1 ? cookie.substring(0, eqPos) : cookie
                document.cookie =
                    name + "=;expires=Thu, 01 Jan 1970 00:00:00 GMT;path=/"
            }
            localStorage.clear()
            location.href = "/users/auth/login"
        }
    }
</script>

<div class="mainDiv">
    <form
        on:submit|preventDefault={handleSubmit}
        class="grid w-[100%] items-center gap-2"
    >
        <textarea
            class="w-[100%] bg-slate-950 border-2 border-white rounded-md h-20 resize-none p-2"
            placeholder="Comment your thoughts"
            bind:this={textArea}
        />
        <input
            type="submit"
            value="COMMENT!!"
            class="bg-quacker p-4 border rounded-md border-none"
        />
    </form>
</div>

<style>
</style>
