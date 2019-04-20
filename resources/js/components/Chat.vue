<template>
    <div class="chat-app">
        <Conversation :contact="selectedContact" :messages="messages" @new="saveNewMessage"/>
        <ContactList :contacts="contacts" @selected="startConversationWith"/>
    </div>
</template>

<script>
    import Conversation from './Conversation';
    import ContactList from './ContactList';

    export default {
        props: {
            user: {
                typ: Object,
                required: true
            }
        },
        data() {
            return {
                selectedContact: null,
                messages: [],
                contacts: []
            }
        },
        mounted() {
            Echo.private(`messages.${this.user.id}`)
                .listen('NewMessage', (e) => {
                    this.handleIncoming(e.message);
                });
            let self = this;
            axios.get('/contacts')
                .then(function (response) {
                    self.contacts = response.data;
                })
        },
        methods: {
            startConversationWith(contact) {
                let self = this;
                axios.get(`/conversation/${contact.id}`)
                    .then(function (response) {
                            self.messages = response.data;
                            self.selectedContact = contact;
                        }
                    );
            },
            saveNewMessage(text) {
                this.messages.push(text);
            },
            handleIncoming(message) {
                if (this.selectedContact && message.from == this.selectedContact.id) {
                    this.saveNewMessage(message);
                    // return;
                }

                alert(message.text);
            }
        },
        components: {
            Conversation,
            ContactList
        },
    }
</script>
<style lang="scss" type="text/scss" scoped>
    .chat-app {
        display: flex;
    }
</style>
