<script>
import axios from "axios";

axios.defaults.withCredentials = true;
axios.defaults.baseURL = "http://localhost:8000";
// Aggiungi il token CSRF come header di default

export default {
    data() {
        return {
            userEmail: "",
            userPassword: "",
            loggedIn: false,
        };
    },
    mounted() {
        this.verifySession();
    },

    methods: {
        userLogin() {
            axios.defaults.headers.common["X-XSRF-TOKEN"] =
                this.getCsrfTokenFromCookies();
            axios.get("http://localhost:8000/sanctum/csrf-cookie");

            axios
                .post("http://localhost:8000/login", {
                    email: this.userEmail,
                    password: this.userPassword,
                })
                .then((response) => {
                    console.log("Login riuscito:", response.data);
                    this.loggedIn = true;
                    this.userEmail = "";
                    this.userPassword = "";
                })
                .catch((error) => {
                    console.error(
                        "Errore durante il login:",
                        error.response.data
                    );
                });
        },

        userLogout() {
            axios.defaults.headers.common["X-XSRF-TOKEN"] =
                this.getCsrfTokenFromCookies();
            axios.post("http://localhost:8000/logout").then((response) => {
                console.log("Logout riuscito");
                this.loggedIn = false;
            });
        },

        getCsrfTokenFromCookies() {
            const matches = document.cookie.match(/XSRF-TOKEN=([^;]+)/);
            return matches ? decodeURIComponent(matches[1]) : null;
        },

        verifySession() {
            axios
                .get("http://localhost:8000/api/user")
                .then((response) => {
                    this.loggedIn = true;
                    console.log("Hello");
                })
                .catch((error) => {
                    this.loggedIn = false;
                });
        },
    },
};
</script>

<template>
    <div
        class="flex min-h-full flex-col justify-center px-6 pt-48 pb-10 lg:px-8"
        v-if="!loggedIn"
    >
        <div class="sm:mx-auto sm:w-full sm:max-w-sm">
            <h2
                class="mt-10 text-center text-2xl/9 font-bold tracking-tight text-gray-900"
            >
                Accedi al Back Office
            </h2>
        </div>

        <div class="mt-10 sm:mx-auto sm:w-full sm:max-w-sm">
            <form class="space-y-6" @submit.prevent="userLogin">
                <div>
                    <label
                        for="email"
                        class="block text-sm/6 font-medium text-gray-900"
                        >Email address</label
                    >
                    <div class="mt-2">
                        <input
                            id="email"
                            name="email"
                            type="email"
                            autocomplete="email"
                            required
                            v-model="userEmail"
                            class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-slate-900 sm:text-sm/6"
                        />
                    </div>
                </div>

                <div>
                    <div class="flex items-center justify-between">
                        <label
                            for="password"
                            class="block text-sm/6 font-medium text-gray-900"
                            >Password</label
                        >
                        <div class="text-sm">
                            <a
                                href="#"
                                class="font-semibold text-slate-900 hover:text-slate-700"
                                >Hai dimenticato la password?</a
                            >
                        </div>
                    </div>
                    <div class="mt-2">
                        <input
                            id="password"
                            name="password"
                            type="password"
                            autocomplete="current-password"
                            required
                            v-model="userPassword"
                            class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-slate-900 sm:text-sm/6"
                        />
                    </div>
                </div>

                <div>
                    <button
                        type="submit"
                        class="flex w-full justify-center rounded-md bg-slate-800 px-3 py-1.5 text-sm/6 font-semibold text-white shadow-sm hover:bg-slate-700 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
                    >
                        Accedi
                    </button>
                </div>
            </form>
        </div>
    </div>

    <div v-if="loggedIn">
        <button
            @click="userLogout"
            class="border p-2 m-3 rounded-lg bg-black text-white"
        >
            Logout
        </button>
    </div>
</template>

<style scoped>
.read-the-docs {
    color: #888;
}
</style>

<!-- Modified file -->