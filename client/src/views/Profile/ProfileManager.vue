<template>
    <v-app>
        <NavBar/>
        <div class="home">
            <div class="profile-container">
                <div class="overlay">
                    <div class="pic">
                        <img style="height: 145px; width: 145px;" :src="require('../../assets/' + this.img)">
                        <div class="name">
                            <h3>{{ getFormattedName(user.firstName, user.lastName) }}</h3>
                        </div>
                    </div>
                </div>
                <div class="nav-bar-setting">
                    <router-link to="/profile"><i class="el-icon-user"></i></router-link> <!-- clubs n activities -->
                    <router-link to="/manager" id="actif"><i class="el-icon-folder"></i></router-link> <!-- manage cna -->
                    <router-link to="/profile-edit"><i class="el-icon-edit"></i></router-link> <!-- edit profile -->
                    <router-link to="/profile-settings"><i class="el-icon-setting"></i></router-link> <!-- self-explanatory-->
                    <a id="log-out" @click="logout()">
                        <v-icon size="20px"> mdi-logout </v-icon>
                    </a>
                </div>
                <div class="content-container">
                    <h1> Manage Your Creation </h1>

                    <span v-if="viewType == true">
                        <div class="card-title">
                            <h2>Clubs</h2>
                            <!-- the icon to switch view -->
                            <div 
                                v-if="viewType == true"
                                class="view-icon" 
                            >
                                <el-tooltip
                                    class="item"
                                    effect="dark"
                                    content="Activity view"
                                    placement="bottom-start"
                                >
                                    <i class="el-icon-sort" @click="viewClick()"></i>
                                </el-tooltip>
                            </div>
                            <div
                                v-else
                                class="view-icon"
                            >
                                <el-tooltip
                                    class="item"
                                    effect="dark"
                                    content="Club view"
                                    placement="bottom-start"
                                >
                                    <i class="el-icon-sort" @click="viewClick()"></i>
                                </el-tooltip>
                            </div>
                            <!-- until here -->
                        </div>
                        <div class="main-container">
                            <div class="cna-view">
                                <div
                                    class="card"
                                    id="smaller-card"
                                    v-for="(club, index) in this.createdClubs"
                                    v-bind:key = "index"
                                >   
                                    <div class="card-content">
                                        <b>{{ club.name }}</b>
                                        <br><br>
                                        
                                    </div>
                                    <div class="Title">
                                        <ClubManagerModal 
                                            v-bind:clubId="club.id"
                                            v-bind:clubName="club.name"
                                        />
                                    </div>
                                </div>
                            </div>
                        </div>
                    </span>

                    <span v-else>
                        <div class="card-title">
                            <h2>Activities</h2>
                            <!-- the icon to switch view -->
                            <div 
                                v-if="viewType == true"
                                class="view-icon" 
                            >
                                <el-tooltip
                                    class="item"
                                    effect="dark"
                                    content="Activity view"
                                    placement="bottom-start"
                                >
                                    <i class="el-icon-sort" @click="viewClick()"></i>
                                </el-tooltip>
                            </div>
                            <div
                                v-else
                                class="view-icon"
                            >
                                <el-tooltip
                                    class="item"
                                    effect="dark"
                                    content="Club view"
                                    placement="bottom-start"
                                >
                                    <i class="el-icon-sort" @click="viewClick()"></i>
                                </el-tooltip>
                            </div>
                            <!-- until here -->
                        </div>
                        <div class="main-container">
                            <div class="cna-view">
                                <div 
                                    class="card" 
                                    id="smaller-card"
                                    v-for="(activity, index) in this.createdActivities" 
                                    v-bind:key = "index"
                                >
                                    <div class="card-content">
                                        <b>{{ activity.name }}</b><br>
                                        Event Date: {{ getFormattedDate(activity.activityDate) }}<br>
                                        Type: {{activity.type}}                                    
                                    </div>
                                    <div class="Title">
                                        <ActivityManagerModal
                                            v-bind:activityId="activity.id"
                                            v-bind:activityName="activity.name"
                                            v-bind:activityType="activity.type"
                                        />
                                    </div>
                                </div>
                            </div>
                        </div>
                    </span>

                </div>
            </div>
        </div>
        <Footer/>
    </v-app>
</template>

<script>
import NavBar from "../NavBar";
import Footer from "../Footer";
import User from "../../models/User";
import ClubManagerModal from "../../components/ClubManagerModal";
import ActivityManagerModal from "../../components/ActivityManagerModal";
import ActivityService from '../../services/activityService';
import ClubService from '../../services/clubService';
import formatter from "../../utils/formatter";

export default {
    data() {
        return {
            user: new User(),
            createdClubs: [],
            createdActivities: [],
            img: '',
            viewType: true,
        }
    },
    components: {
        NavBar,
        Footer,
        ActivityManagerModal,
        ClubManagerModal,
    },
    computed: {
        getCurrentUser() {
            return this.$store.state.auth.status.loggedIn &&
                   this.$store.state.auth.user.user; 
        }
    },
    methods: {
        getFormattedName: (firstName, lastName) => formatter.getFormattedName(firstName, lastName),
        getFormattedDate: (date) => formatter.getFormattedDate(date),
        logout() {
            this.$store.dispatch("auth/logout");
            this.$router.push("/");
        },
        viewClick() {
            this.viewType = !this.viewType;
        }
    },
    async mounted() {
        this.user = this.getCurrentUser;
        if (!this.user) {
            this.$router.push("/");
            return;
        }
        switch(this.user.gender) {
            case "Female":
                this.img = "femaleAvatar.png";
                break;
            default:
                this.img = "maleAvatar.png";
                break;
        }
        this.createdActivities = 
            await ActivityService.getOngoingActivities(this.user.id);
        this.createdClubs =
            await ClubService.getClubs(this.user.id);
    }
}
</script>

<style scoped lang="scss">
.main-container {
    min-height: 0;
}
.card-title {
    border-radius: 10px 10px 0 0;
}
.card-content {
    b {
        font-size: 20px;
    }
}
</style>