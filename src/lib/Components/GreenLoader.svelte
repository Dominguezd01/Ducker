<div class="lds-dual-ring"></div>

<style>
    .lds-dual-ring {
        display: block;
        width: 80px;
        height: 80px;
        margin: 0px auto;
    }
    .lds-dual-ring:after {
        content: " ";
        display: block;
        width: 64px;
        height: 64px;
        margin: 8px;
        border-radius: 50%;
        border: 6px solid #16a34a;
        border-color: #16a34a transparent #16a34a transparent;
        animation: lds-dual-ring 1.2s linear infinite;
    }

    @keyframes lds-dual-ring {
        0% {
            transform: rotate(0deg);
        }
        100% {
            transform: rotate(360deg);
        }
    }
</style>
