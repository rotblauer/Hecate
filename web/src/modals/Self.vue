<template>
    <div class='absolute top left bottom right z3' style="pointer-events: none;">
        <div class='flex-parent flex-parent--center-main flex-parent--center-cross h-full' style="pointer-events: auto;">
            <div class="flex-child px12 py12 w600 h80 bg-white round-ml shadow-darken10">
                <template v-if='error'>
                    <div class='grid w-full col'>
                        <div class='col--12'>
                            <h3 class='w-full py6 txt-m txt-bold align-center'>ERROR!</h3>
                        </div>

                        <div class='col--12 py12' v-text='error'></div>

                        <div class='col--12 py12'>
                            <div class='grid grid--gut12'>
                                <div class='col col--6'></div>
                                <div class='col col--6'>
                                    <button @click='error = false' class='btn round w-full'>Ok</button>
                                </div>
                            </div>
                        </div>
                    </div>
                </template>
                <template v-else>
                    <div class='col col--12'>
                        <div class='col col--12 txt-m txt-bold'>
                            Account Settings
                            <button @click='close' class='fr btn round bg-white color-black bg-darken25-on-hover'><svg class='icon'><use href='#icon-close'/></svg></button>
                        </div>

                        <div class='py6 col col--12 border--gray-light border-b'>
                            <span class='txt-m'>Personal Info</span>
                        </div>

                        <div class='col col--12 py12'>
                            <label>Username</label>
                            <input class='input mb6' v-model='user.username' placeholder='Username' />

                            <label>Email</label>
                            <input class='input mb6' v-model='user.email' placeholder='Email' />
                        </div>

                        <div class='py6 col col--12 border--gray-light border-b'>
                            <span class='txt-m'>Account Security</span>
                        </div>

                        <div class='col col--12 py12'>
                            <label>Password</label>
                            <input type=password disabled class='input mb6' placeholder='Your Password Cannot Be Changed At This Time' />
                        </div>
                    </div>
                </template>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'self',
    data: function() {
        return {
            user: false,
            error: false
        };
    },
    mounted: function() {
        this.getSelf();
    },
    methods: {
        close: function() {
            this.$emit('close');
        },
        getSelf: function() {
            fetch(`${window.location.protocol}//${window.location.host}/api/user/info`, {
                method: 'GET',
                credentials: 'same-origin'
            }).then((response) => {
                if (response.status !== 200) {
                    this.error = response.status + ':' + response.statusText;
                }

                return response.json();
            }).then((user) => {
                this.user = user;
            }).catch((err) => {
                this.error = err.message;
            });
        },
    },
    render: h => h(App),
    props: ['auth']
}
</script>
