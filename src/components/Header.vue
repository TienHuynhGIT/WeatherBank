<template>
    <div class="nav-bar">
        <b-nav>
            <img src="@/assets/navlogo.png"/>
            <b-nav-item>
                <router-link to="/">Home</router-link>
            </b-nav-item>
            <b-nav-item>
                <router-link to="/privacy">Privacy</router-link>
            </b-nav-item>
        </b-nav>
        <img src="@/assets/logo.png"/>
    </div>
</template>

<script>
    export default {
        name: 'Header',
    }
</script>


<style scoped>
    a {
        text-decoration: none;
        color: black;
    }
    a:hover {
        color: black;
    }

    .nav-bar {
        padding-top: 10px;
    }
</style>
