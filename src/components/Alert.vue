<template>
    <span :class="`alert ${isShow ? 'fade' : ''}`">
        <slot />
    </span>
</template>

<script>
export default {
    props: {
        isShow: Boolean
    }
}
</script>

<style lang="scss" scoped>
.alert {
    display: block;
    width: fit-content;
    margin: 0 auto 1em;
    font-size: 1.5rem;
    color: white;
    padding: .5em 2em;
    background-color: rgb(70, 63, 51);
    border-radius: 2em;
    opacity: 0%;
}

.fade {
    animation-name: fade-animation;
    animation-duration: 2s;
    animation-direction: alternate;
}

@keyframes fade-animation {
    0% {
        opacity: 0%;
    }
    50% {
        opacity: 100%;
    }
}
</style>