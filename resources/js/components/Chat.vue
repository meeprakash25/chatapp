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
                this.updateUnreadContact(contact, true);
                let self = this;
                axios.get(`/conversation/${contact.id}`)
                    .then(function (response) {
                            self.messages = response.data;
                            self.selectedContact = contact;
                        }
                    );
            },
            saveNewMessage(message) {
                this.messages.push(message);
            },
            handleIncoming(message) {
                if (this.selectedContact && message.from == this.selectedContact.id) {
                    this.saveNewMessage(message);
                    return;
                }

                this.updateUnreadContact(message.from_contact,false);
            },
            updateUnreadContact(contact, reset) {
                this.contacts = this.contacts.map((single) => {
                    if (single.id !== contact.id) {
                        return single;
                    }
                    if (reset)
                        single.unread = 0;
                    else
                        single.unread += 1;
                    return single;
                })
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
