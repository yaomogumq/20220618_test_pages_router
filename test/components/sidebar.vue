<template>
    <link href="https://fonts.googleapis.com/css2?family=Material+Icons" rel="stylesheet">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Fira+Sans:ital,wght@0,400;0,700;0,900;1,400;1,700;1,900&display=swap" rel="stylesheet">
    
    <aside :class="`${is_expanded && 'is-expanded'}`">
            <div class="logo">
                <img src="../assets/BIMCAP_Blue.png" alt="bimcap">
            </div>
            <div class="menu-toggle-wrap">
                <button class="menu-toggle" @click="ToggleMenu">
                    <span class="material-icons">keyboard_double_arrow_right</span>
                </button>
            </div>

            
            <div class="menu">
                <NuxtLink class="button" to="/">
                    <span class="material-icons">home</span>
                    <span class="text">home</span>
                </NuxtLink>
                <NuxtLink class="button" to="/publish">
                    <span class="material-icons">publish</span>
                    <span class="text">publish</span>
                </NuxtLink>
                <NuxtLink class="button" to="/schedule">
                    <span class="material-icons">calendar_month</span>
                    <span class="text">schedule</span>
                </NuxtLink>
            </div>
    </aside>
    

</template>


<script setup>
import {ref} from'vue'

const is_expanded =ref(false)

const ToggleMenu = () => {
    is_expanded.value =! is_expanded.value
}
</script>

<style lang="scss" scoped>
    aside {
        display: flex;
        flex-direction: column;
        background-color: var(--greyer);
        color: var(--light);
        width: calc(2.2rem + 2rem);
        overflow: hidden;
        min-height: 100vh;
        padding: 1rem;
        transition: 0.2s ease-in-out;
        .flex {
            flex: 1 1 0%;
        }
        .logo {
            margin: 0rem 0rem 0rem -.8rem;
            margin-bottom: 1rem;
            img {
                width: 4rem;
                height: 1.4rem;
            }
        }
        .menu-toggle-wrap {
            display: flex;
            justify-content: flex-end;
            margin-bottom: 1rem;
            position: relative;
            top: 0;
            transition: 0.2s ;
            .menu-toggle {
                transition: 0.2s ease-in-out;
                .material-icons {
                    font-size: 2rem;
                    color: var(--light);
                    transition: 0.2s ease-out;
                }
                
                &:hover {
                    .material-icons {
                        color: var(--primary);
                        transform: translateX(0.5rem);
                    }
                }
            }
        }
        h3, .button .text {
            opacity: 0;
            transition: opacity 0.3s ease-in-out;
        }
        h3 {
            color: var(--grey);
            font-size: 0.875rem;
            margin-bottom: 0.5rem;
            text-transform: uppercase;
        }
        .menu {
            margin: 0 -1rem;
            .button {
                display: flex;
                align-items: center;
                text-decoration: none;
                transition: 0.2s ease-in-out;
                padding: 0.5rem 1rem;
                .material-icons {
                    font-size: 2rem;
                    color: var(--light);
                    transition: 0.2s ease-in-out;
                }
                .text {
                    color: var(--light);
                    transition: 0.2s ease-in-out;
                }
                &:hover {
                    background-color: var(--dark-alt);
                    .material-icons, .text {
                        color: var(--primary);
                    }
                }
                &.router-link-exact-active {
                    background-color: var(--dark-alt);
                    border-right: 5px solid var(--primary);
                    .material-icons, .text {
                        color: var(--primary);
                    }
                }
            }
        }
        .footer {
            opacity: 0;
            transition: opacity 0.3s ease-in-out;
            p {
                font-size: 0.875rem;
                color: var(--grey);
            }
        }
        &.is-expanded {
            width: var(--sidebar-width);
            .menu-toggle-wrap {
                top: -2rem;
                
                .menu-toggle {
                    transform: rotate(-180deg);
                }
            }
            h3, .button .text {
                opacity: 1;
            }
            .button {
                .material-icons {
                    margin-right: 1rem;
                }
            }
            .footer {
                opacity: 0;
            }
        }
        @media (max-width: 768px) {
            position: fixed;
            z-index: 99;
        }
    }
</style>
