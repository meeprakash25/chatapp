<template>
    <div class="contact-list">
        <ul>
            <li v-for="(contact,index) in contacts" :key="contact.id" @click="selectedContact(index,contact)"
                :class="{'selected':index===selected}">
                <div class="avatar">
                    <img :src="contact.profile_image" :alt="contact.name">
                </div>
                <div class="contact">
                    <p class="name">{{contact.name}}</p>
                    <p class="email">{{contact.email}}</p>
                </div>
            </li>
        </ul>
    </div>
</template>

<script>
    export default {
        props: {
            contacts: {
                type: Array,
                default: []
            }
        },
        data() {
            return {
                selected: 0
            }
        },
        methods: {
            selectedContact(index, contact) {
                this.selected = index;
                this.$emit('selected', contact);
            }
        }
    }
</script>

<style lang="scss" type="text/scss" scoped>
    .contact-list {
        flex: 2;
        max-height: 450px;
        overflow-y: scroll;
        border-left: 1px solid lightgray;

        ul {
            list-style-type: none;
            padding-left: 0;

            li {
                height: 80px;
                padding: 2px;
                position: relative;
                border-bottom: 1px solid lightgray;
                cursor: pointer;
                display: flex;

                &:hover {
                    background-color: #E1E1E1;
                }

                &.selected {
                    background-color: lightgray;
                }

                .avatar {
                    display: flex;
                    flex: 1;
                    align-items: center;

                    img {
                        width: 35px;
                        border-radius: 50%;
                        margin: 0 auto;
                    }
                }

                .contact {
                    flex: 3;
                    font-size: 10px;
                    overflow: hidden;
                    display: flex;
                    flex-direction: column;
                    justify-content: center;

                    p {
                        margin: 0;

                        &.name {
                            font-weight: bold;
                        }
                    }
                }

            }
        }
    }
</style>
