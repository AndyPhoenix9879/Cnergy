<template>
    <v-app>
        <NavBar/>
        <div class="home">

            <div class="profile-container">

                <div class="overlay">
                    <div class="pic">
                        <img style="height: 145px; width: 145px;" :src="require('../../assets/'+img)">
                        <div class="name">
                            <h3>{{ getFormattedName(user.firstName, user.lastName) }}</h3>
                        </div>
                    </div>
                </div>

                <div class="nav-bar-setting">
                    <router-link to="/profile"><i class="el-icon-user"></i></router-link>
                    <router-link to="/manager"><i class="el-icon-folder"></i></router-link>
                    <router-link to="/profile-edit" id="actif"><i class="el-icon-edit"></i></router-link>
                    <router-link to="/profile-settings"><i class="el-icon-setting"></i></router-link>
                    <a id="log-out" @click="logout()">
                        <v-icon size="20px"> mdi-logout </v-icon>
                    </a>
                </div>                
                <div class="content-container">
                    <h1> Edit Your Profile </h1>
                    <div class="flex-container">
                        <div class="card-container" id="desc">
                            <h2> About me </h2>
                            <div class="content-lists">
                                <textarea
                                    rows="5"
                                    type="text"
                                    class="input-box-desc"
                                    v-model="user.about"
                                    placeholder="About Me"
                                />
                            </div>
                        </div>
                    </div>
                    <button
                        id="one"
                        @click="changeAbout()"
                    >
                        Edit
                    </button>
                </div>
            </div>
        </div>
        <Footer/>
    </v-app>
</template>

<script>
import NavBar from "../NavBar"
import Footer from "../Footer"
import User from "../../models/User";
import formatter from  "../../utils/formatter";
import alerter from '../../utils/alerter';

export default {
    data() {
        return {
            user: new User(),
            img: '',
        }
    },
    components: {
        NavBar,
        Footer,
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
        getSIDFromEmail: (email) => email.split("@")[0],
        changeAbout() {
            this.$store
                .dispatch(
                    "user/updateAbout",
                    [this.user.id, this.user.about],
                )
                .then(
                    response => response.data.status == 200
                        ?   this.$fire(alerter.successAlert(
                                "Update About Success"
                            ))
                        :   this.$fire(alerter.errorAlert(
                                "Update About Failed"
                            )),
                );
        },
        logout() {
            this.$store.dispatch("auth/logout");
            this.$router.push("/");
        },
    },
    mounted() {
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
    }
}
</script>

<style scoped lang="scss">
#whiteBtn {
    border: 2px solid white;
    color: white;
}
#edit {
    text-align: left;
    padding: 20px;
    padding-left: 30px;
}
#one {
    margin-top: 10px;
    width: 100px;
    background-color: white;
}
.card-container {
    text-align: left;
}
.input-box-desc {
    width: 100%;
    outline: none;
}
.content-lists {
    display: flex;
    margin: 10px;
    textarea {
        resize: none;
    }
}
label {
    font-size: 17px;
    font-weight: bold;
    flex: 2;
}
input {
    flex: 5;
    outline: none;
}
button {
    display: inline-block;
    width: 70px;
    border-radius: 15px;
    outline: none;
    border: 2px solid var(--accent-color);
    box-shadow:  0px 3px silver;
    color: var(--accent-color);
    margin: 0 auto 0 auto;
    cursor: pointer;
    transition: .1s;
}
button:hover {
    color: black;
    background-color: var(--accent-color);
}
button:active {
  box-shadow: 0 1px silver;
  transform: translateY(3px);
}
</style>