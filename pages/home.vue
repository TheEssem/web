<template>
    <div class="home" v-if=user>
        <div class="home-room-wrapper">
            <div class="in-room" v-if=room>
                <h1 class="title">Let's jump back into watching</h1>
                <p class="subtitle">You're already in a room. Click the button below to get back into the room.<br>Want out? There's an option for that too</p>
                <div class="options has-link is-horizontal">
                    <nuxt-link to="/room" :class="{ 'disabled': leavingRoom }">
                        <div class="box option join-room" :class="{ 'disabled': leavingRoom }">
                            <div class="option-inner">
                                <div class="portal-indicator loading" v-if="room.portal.status === 'open'"></div>
                                <img src="/icons/tv.svg" class="icon">
                                <h3 class="header">Return to {{ room.name }}</h3>
                            </div>
                        </div>
                    </nuxt-link>
                    <nuxt-link to="" :class="{ 'disabled': leavingRoom }" @click.native=leaveRoom()>
                        <div class="box option join-room" :class="{ 'loading': leavingRoom, 'disabled': leavingRoom }">
                            <div class="option-inner">
                                <img src="/icons/panel-arrow-right.svg" class="icon">
                                <h3 class="header">{{ leavingRoom ? 'Leaving' : 'Leave' }} {{ room.name }}{{ leavingRoom ? '...' : '' }}</h3>
                            </div>
                        </div>
                    </nuxt-link>
                </div>
            </div>
            <div class="no-room" v-else-if=!room>
                <h1 class="title">Welcome Home</h1>
                <p class="subtitle">It's never been easier to join or start a room with your friends.<br>Select an option below to get started</p>
                <div class="options has-link is-horizontal">
                    <nuxt-link to="#join-room" @click.native=showJoinRoomModal()>
                        <div class="box option join-room">
                            <div class="option-inner">
                                <img src="/icons/panel-arrow-right.svg" class="icon">
                                <h3 class="header">Join a Room</h3>
                                <p class="description">Received an invite link or an invite code for a room? Enter it here!</p>
                            </div>
                        </div>
                    </nuxt-link>
                    <nuxt-link to="#create-room" @click.native=showCreateRoomModal()>
                        <div class="box option create-room">
                            <div class="option-inner">
                                <img src="/icons/add.svg" class="icon">
                                <h3 class="header">Create a Room</h3>
                                <p class="description">Need a room where you can watch anything with your friends? This is the place to go</p>
                            </div>
                        </div>
                    </nuxt-link>
                </div>
            </div>
            <Modal ref="createRoomModal" :cover="true">
                <CreateRoom :modal="true" />
            </Modal>
            <Modal ref="joinRoomModal" :cover="true">
                <JoinRoom :modal="true" />
            </Modal>
        </div>
    </div>
</template>
<script>
    import { mapGetters } from 'vuex'

    import Modal from '~/components/modal/index'
    import JoinRoom from '~/components/room/join'
    import CreateRoom from '~/components/room/create'

    import Input from '~/components/input'
    import Button from '~/components/button'

    export default {
        head() {
            return {
                title: (
                    this.isCreateRoomModalVisible ?
                    'Create a Room' :
                    (
                        this.isJoinRoomModalVisible ?
                        'Join a Room' :
                        null
                    )
                )
            }
        },
        middleware: 'authenticated',
        computed: {
            ...mapGetters(['user']),
            room() {
                return this.$store.state.room || this.user.room || null
            },

            isJoinRoomModalVisible() {
                if(!this.$refs.joinRoomModal) return false

                return this.$refs.joinRoomModal.visible
            },
            isCreateRoomModalVisible() {
                if(!this.$refs.createRoomModal) return false

                return this.$refs.createRoomModal.visible
            }
        },
        methods: {
            leaveRoom() {
                if(!confirm('Are you sure you want to leave this room? Once you leave this room, you cannot join back without an invite')) return

                this.leavingRoom = true
                this.$store.dispatch('leaveRoom')
            },

            showCreateRoomModal() {
                if(!this.$refs.createRoomModal) return

                this.$refs.createRoomModal.visible = true
            },
            showJoinRoomModal() {
                if(!this.$refs.joinRoomModal) return

                this.$refs.joinRoomModal.visible = true
            },
            hideModals() {
                if(!this.$refs.joinRoomModal && !this.$refs.joinRoomModal) return

                this.$refs.createRoomModal.visible = false
                this.$refs.joinRoomModal.visible = false
            }
        },
        data() {
            return {
                leavingRoom: false
            }
        },
        mounted() {
            if(!this.$refs.joinRoomModal && !this.$refs.joinRoomModal) return

            this.$refs.createRoomModal.visible = this.$route.hash === '#create-room'
            this.$refs.joinRoomModal.visible = this.$route.hash === '#join-room'
        },
        components: {
            Input,
            Button,

            Modal,
            JoinRoom,
            CreateRoom
        }
    }
</script>

<style src="~/static/css/home.css" scoped></style>
