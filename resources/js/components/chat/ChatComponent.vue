<template>
    <div>
        <div class="dc-dashboardboxtitle dc-titlemessages" v-if="user">
            <a href="javascript:void(0);" class="dc-back"><i class="ti-arrow-left"></i></a>
            <div class="dc-userlogedin">
                <figure class="dc-userimg">
                    <img :src="user.selected_user_image" alt="trans('lang.img_desc)">
                </figure>
                <div class="dc-username">
                    <h3><i class="fa fa-check-circle"></i> {{user.selected_user_name}}</h3>
                    <span>{{user.selected_user_tagline}}</span>
                </div>
            </div>
            <a :href="url+'/profile/'+user.selected_user_slug" class="dc-viewprofile">{{ trans('lang.view_profile') }}</a>
        </div>
        <div class="dc-dashboardboxcontent dc-dashboardholder dc-offersmessages">
            <ul v-if="users">
                <li>
                    <div class="dc-verticalscrollbar dc-dashboardscrollbar">
                        <chat-users></chat-users>
                    </div>
                </li>
                <li>
                    <chat-area :empty_error="this.empty_field" :chat_host="this.host" :chat_port="this.port"></chat-area>
                </li>
            </ul>
            <div class="dc-chatarea dc-chatarea-empty" v-else>
                <figure class="dc-chatemptyimg">
                    <img :src="no_record_img" alt="trans('lang.img_desc)">
                    <figcaption><h3>{{ trans('lang.no_chat_message') }}</h3></figcaption>
                </figure>
            </div>
        </div>
    </div>
</template>
<script>
import Event from '../../event.js'
    export default{
        props:['no_msg', 'empty_field', 'host', 'port'],
        data() {
            return {
                users: true,
                url:APP_URL,
                no_record_img: APP_URL+'/images/message-img.png',
                chat_users:[],
                id:'',
                user:'',
            }
        },
        methods: {
            getUsers(){
                let self = this;
                axios.get(APP_URL + '/message-center/get-users')
                .then(function (response) {
                    if (response.data.type == 'error') {
                        self.users = false;
                        Event.$emit('chat-users', { users:self.chat_users });
                    } else {
                        Event.$emit('chat-users', { users:response.data });
                    }
                });

            },
        },
        mounted() {
            Event.$on('active-user', (data) => {
                this.id = data.id;
                this.user = data.user;
            })
        },
        created: function () {
            this.getUsers();
        }
    }
</script>
