<template>
    <div class="text-center">
        <button @click="opendialog()">More info</button>
        <v-dialog
            v-model="dialog"
            width="500"
        >
            <v-card>
                <v-card-title
                    class="headline"
                    primary-title
                > 
                    {{ data.name }}
                </v-card-title>
                <v-card-text
                    v-if="context == `club`"
                >
                    {{ data.description }}
                </v-card-text>
                <v-card-text
                    v-else
                >
                    <b>Description</b><br>
                    {{ data.description }}<br>

                    <b>Date</b>
                    {{ getFormattedDate(data.activityDate) }}<br>
                    
                    <b>Minimum Participants: </b>{{ data.minParticipants }}<br>
                    <b>Maximum Participants: </b>{{ data.maxParticipants }}<br>
                    <b>Activity Type: </b>{{ data.type }}<br>
                </v-card-text>
                <v-divider/>
                <v-card-actions v-if="context == `club`">
                    <v-spacer/>
                    <button
                        id="greenbtn"
                        @click="reasonDialog = true"
                    > Join
                    </button>
                    <v-dialog
                        v-model="reasonDialog"
                        width="500"
                    >
                        <v-card>
                            <v-card-title
                                class="headline"
                                primary-title
                            > 
                                Why do you wish to join us?
                            </v-card-title>
                            <textarea 
                                rows="6"
                                v-model="reason"
                                placeholder="Tell us the reason why you would like to join us" 
                            />
                            <v-divider/>
                            <v-card-actions>
                                <v-spacer/>
                                <button 
                                    id="greenbtn"
                                    @click="handleJoinClub"
                                > 
                                    Submit
                                </button>
                                <v-spacer/>
                                <button @click="reasonDialog = false"> Back </button>
                                <v-spacer/>
                            </v-card-actions>
                        </v-card>
                    </v-dialog>
                    <v-spacer/>
                    <button @click="dialog = false"> Ok </button>
                    <v-spacer/>
                </v-card-actions>
                <v-card-actions 
                    v-else>
                    <v-spacer/>
                    <button
                        id="greenbtn"
                        @click="handleJoinActivity"
                    > Join
                    </button>
                    <v-spacer/>
                    <button @click="dialog = false"> Ok </button>
                    <v-spacer/>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </div>
</template>

<script>
import ActivityService from "../services/activityService";
import ClubService from "../services/clubService";
import User from "../models/User";
import alerter from '../utils/alerter';
import formatter from '../utils/formatter';

export default {
    data () {
        return {
            user: new User(),
            reason: "",
            dialog: false,
            reasonDialog: false,
        }
    },
    props: {
        data: { type: Object },
        context: { type: String },
    },
    computed: {
        getCurrentUser() {
            return this.$store.state.auth.status.loggedIn &&
                   this.$store.state.auth.user.user; 
        }
    },
    methods: {
        getFormattedDate: (date) => formatter.getFormattedDate(date), 
        opendialog() {
            this.dialog = true;
        },
        async handleJoinClub() {
            let response =
                await ClubService
                    .joinClub(this.user.id, this.data.id, this.reason)
                    .then(response => response.status == 200
                        ?   this.$fire(alerter.successAlert(
                                "Signed Up Success",
                                response.data.message
                            ))
                        :   this.$fire(alerter.errorAlert(
                                "Signed Up Failed",
                                response.data.message
                            )));
            this.reasonDialog = true
        },
        async handleJoinActivity() {
            let response = 
                await ActivityService
                    .joinActivity(this.user.id, this.data.id)
                    .then(response => response.status == 200
                        ?   this.$fire(alerter.successAlert(
                                "Signed Up Success",
                                response.data.message
                            ))
                        :   this.$fire(alerter.errorAlert(
                                "Signed Up Failed",
                                response.data.message
                        )));
            this.reasonDialog = true
        },
        handleJoin() {
            if (this.context == "club") {
                this.handleJoinClub();
                return;
            }
            this.handleJoinActivity();
        }
    },
    mounted() {
        this.user = this.getCurrentUser;
    }
}
</script>

<style scoped lang="scss">
#info-btn {
    position: relative;
}
.text-center {
    z-index: 9999;
}
#greenbtn {
    border: 2px solid #67C23A;
    color: #67C23A;
}
#greenbtn:hover {
    background-color: #67C23A;
    color: white;
}

button{
    padding: 5px 30px;
    border-radius: 50px;
    outline: none;
    border: 2px solid #4285F4;
    box-shadow:  0px 3px silver;
    color: #4285F4;
    cursor: pointer;
    transition: .1s;
}
button:hover {
    background-color: #4285F4;
    color: #fff;
}
button:active {
    box-shadow: 0 1px silver;
    transform: translateY(3px);
}
</style>