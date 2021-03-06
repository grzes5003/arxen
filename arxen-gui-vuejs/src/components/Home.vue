<template>
    <div class="card-window" :style="[{ height }, cssVars]">
        <div class="chat-container">
            <b-navbar toggleable="sm" type="light" variant="light" class="b-navbar">
                <b-nav-item class="b-nav-item">
                    <svg-icon name="dropdown" :param="true"/>
                </b-nav-item>
                <b-nav-item class="b-nav-item">
                    <svg-icon name="send" :param="true"/>
                </b-nav-item>
                <b-nav-item class="b-nav-item">
                    <svg-icon name="eye" :param="true"/>
                </b-nav-item>
            </b-navbar>

            <app-chat-list class="col-2"
                           @selectDiffrentChat="updateSelectedChat"
                           :selectedChatId="selectedChatId"
            />

            <app-message-list :selectedChatId="selectedChatId"
                              :textMessages="t"
                              v-if="selectedChatId"
                              :userName="getUserName"
            />
        </div>
    </div>
</template>

<script>
    import ChatList from '@/components/ChatList.vue';
    import MessageList from '@/components/MessageList.vue';
    //import MessageForm from '@/components/MessageForm.vue';
    import gql from 'graphql-tag';
    import {defaultThemeStyles, cssThemeVars} from '../themes';
    import locales from '../locales'
    import {BNavItem, BNavbar} from 'bootstrap-vue'
    import SvgIcon from "@/components/SvgIcon";

    export default {
        components: {
            'svg-icon': SvgIcon,
            'b-navbar': BNavbar,
            'b-nav-item': BNavItem,
            'app-chat-list': ChatList,
            'app-message-list': MessageList,
            //'app-message-form': MessageForm,
        },
        props: {
            height: {type: String, default: '600px'},
            theme: {type: String, default: 'light'},
            styles: {type: Object, default: () => ({})},
        },
        data() {
            return {
                chatList: [],
                selectedChatId: '',
                getUserName: [],
            };
        },
        apollo: {
            getUserName() {
                return {
                    query: gql`
                    query { getUserName }`,
                    variables() {
                    },
                };
            },
        },
        computed: {
            t() {
                return {
                    ...locales,
                    ...this.textMessages
                }
            },
            cssVars() {
                const defaultStyles = defaultThemeStyles[this.theme];
                const customStyles = {};
                Object.keys(defaultStyles).map(key => {
                    customStyles[key] = {
                        ...defaultStyles[key],
                        ...(this.styles[key] || {})
                    }
                });
                return cssThemeVars(customStyles);
            }
        },
        methods: {
            updateSelectedChat(chatID) {
                this.selectedChatId = chatID;
            }
        }
    };
    // <app-message-form :selectedChatId="selectedChatId"
    //:textMessages="t"/>
</script>

<style lang="scss">
    @import '../styles/index.scss';
    @import url('https://fonts.googleapis.com/css2?family=Quicksand:wght@500&display=swap');

    * {
        font-family: 'Quicksand', sans-serif;
    }

    .card-window {
        width: 100%;
        display: block;
        max-width: 100%;
        background: var(--chat-content-bg-color);
        color: var(--chat-color);
        overflow-wrap: break-word;
        position: relative;
        white-space: normal;
        border: var(--chat-container-border);
        border-radius: var(--chat-container-border-radius);
        box-shadow: var(--chat-container-box-shadow);
    }

    .chat-container {
        height: 100%;
        display: flex;

        textarea,
        input[type='text'],
        input[type='search'] {
            -webkit-appearance: none;
        }
    }

    .b-navbar {
        list-style: none;
        padding: 0px;
        margin: 0px;
        display: flex;
        flex-direction: column;
        align-items: center;
        height: 100%;
    }

    .b-navbar:last-child {
        margin-top: auto;
    }

    .b-nav-item {
        padding: 5px;
        display: flex;
        align-items: center;
        text-decoration: none;

        &:hover {
            background: var(--custom-menu-sidemenu-bg-color-hover);
            transition: background-color 0.3s cubic-bezier(0.25, 0.8, 0.5, 1);
        }
        &:not(:hover) {
            transition: background-color 0.3s cubic-bezier(0.25, 0.8, 0.5, 1);
        }
    }

    .b-nav-item svg {
        margin: 0 7px;
    }

</style>
