<template>
<div>  
    <navigation />
<div class=background-image-profile>
    <img :src= "require(`@/data/img/sunsetdogs.jpg`)" id="sunsetdogs">
    <div class=image-profile-text>
    <h1 class="center new-font"> WELCOME, {{username}}! </h1>
    <div id="profile">
        <div id="pets-parent">
                <h2 id="new-font">MY PET FAMILY!</h2>
                    <div id="forms">
                    <RegisterPet v-show="addPet" v-on:toggleForm="toggleForm"/>
                    </div>
                    <div id="pet-card">
                    <ProfilePets v-if="viewPets" v-bind:username="username" v-on:userPets="assignPetArray"/>
            <div><button id="red-button" v-on:click="addPet = true, viewPets=false, addPlaydate=false"> Register a New Pet! </button></div> 
                    </div>
             </div>
    
        <div id="playdates-parent">
            <div class= "playdate-buttons">
            <button v-show="showRequests" id="requests-button" class="borderBlink"> <router-link :to="{ name: 'playdateRequests'}"> You Have New Playdate Requests! <br><span class="smaller"> view now </span></router-link> </button>
            <check-for-requests v-on:hasRequests="hasRequests"/>
            </div>
            <div id="playdates">
            <CreatePlaydate v-if="addPlaydate" v-on:toggleThisForm="showProfile" v-bind:userPets="arrayOfPets"/>
                <div v-if="viewPlaydates">               
                    <h1 class="new-font"> MY PLAYDATES </h1>
                    <div class="card" v-for="playdate in userPlaydates" :key="playdate.playdateId">
                        <div class="playdate">
                            <div id="user">{{playdate.username}}</div>
                            <div id="time">{{playdate.playdateDate}} at {{playdate.playdateTime}}</div>
                            <div id="space"></div>
                            <div id="address">
                                {{playdate.address}}<br>
                                {{playdate.city}}, {{playdate.region}} {{playdate.zipcode}}
                            </div>
                        </div>
                        <div id="buttons">
                            <router-link :to="{name: 'playdateDetail', params: {id: playdate.playdateId}}" tag="button" class="playdates-button"> Details/Chat </router-link><br>
                            <button v-on:click.prevent="editPlaydate = true, viewPlaydates = false; selectedPlaydate = playdate" class="playdates-button"> Edit </button><br>
                            <button v-on:click.prevent="addPetToPlaydate = true; viewPlaydates = false; selectedPlaydate = playdate" class="playdates-button"> Add pet </button><br>
                        </div>
                    </div>
            <button id="red-button" v-if="!addPlaydate" v-on:click="addPlaydate=true"> Create New Playdate! </button>
                </div>
                <AddPetToPlaydate v-if="addPetToPlaydate" v-on:resetpage="resetPage" v-bind:initial="selectedPlaydate" v-bind:userPets="arrayOfPets"/>
                <EditPlaydate v-if="editPlaydate" v-on:togglePlaydateEdit="togglePlaydateEdit" v-on:togglePlaydateDelete="togglePlaydateDelete" v-bind:initial="selectedPlaydate"/>
                <MySignedUpPlaydates/>
            </div>
        </div>
        </div>
    </div>
    </div>
</div>
</template>

<script>
//Hello World
import authLib from '@/auth';
import RegisterPet from '@/component/RegisterPet';
import ProfilePets from '@/component/ProfilePets';
import CreatePlaydate from '@/component/CreatePlaydate';
import EditPlaydate from '@/component/EditPlaydate';
import Navigation from '@/component/Nav';
import AddPetToPlaydate from '@/component/AddPetToPlaydate';
import MySignedUpPlaydates from '@/component/MySignedUpPlaydates';
import CheckForRequests from '@/component/CheckForRequests';

export default {
    data(){
        return{
            selectedPlaydate: {},
            username: "",
            addPet: false,
            viewPets: true,
            viewPlaydates: true,
            addPlaydate: false,
            editPlaydate: false,
            addPetToPlaydate: false,
            userPlaydates: [],
            arrayOfPets: [],
            showRequests: false
        }
    },
    methods: {
        hasRequests(value){
            if(value){
                this.showRequests = true;
            } else{
                this.showRequests = false;
            }
        },
        resetPage() {
            this.addPet = false;
            this.viewPets = true;
            this.viewPlaydates = true;
            this.addPlaydate = false;
            this.editPlaydate = false;
            this.addPetToPlaydate = false;
            this.showPlaydates();
        },
        showProfile() {
            this.addPet = false;
            this.addPlaydate = false;
            this.editPlaydate = false;
            this.addPetToPlaydate = false;
            this.viewPets = true;
            this.viewPlaydates = true;
            this.showPlaydates();
        },
        assignPetArray(petArray) {
            this.arrayOfPets = petArray;
        },
        toggleForm(value){
            this.addPet = value;
            this.addPlaydate = false;
            this.viewPets = true;
        },
        toggleAddPetToPlaydate() {
            this.addPetToPlaydate = !this.addPetToPlaydate;
        },
        togglePlaydateEdit() {
            this.editPlaydate = false;
            this.viewPlaydates = true;
        },
        togglePlaydateDelete(item) {
            let index = this.userPlaydates.indexOf(item);
            this.userPlaydates.splice(index, 1);
            this.showPlaydates();
        },
        showPlaydates(){
            fetch(`${process.env.VUE_APP_REMOTE_API}/profilePlaydates/${this.username}`,
                {
                    method: 'GET',
                    headers: {
                    Authorization: "Bearer " + authLib.getToken(),
                    Accept: "application/json",
                    "Content-Type": "application/json"
                },
            })

            .then( (response) => {return response.json();} )
            .then( (playdateData) => {
                this.userPlaydates = playdateData;
            })
            .catch((err) => {console.error(err + 'you have no playdates to view.');})
        },
    },
    components: {
        Navigation,
        RegisterPet,
        ProfilePets,
        CreatePlaydate,
        EditPlaydate,
        AddPetToPlaydate,
        MySignedUpPlaydates,
        CheckForRequests
    },
    created(){
        this.username = authLib.getUser().sub;
        this.showPlaydates();
    }
}
</script>

<style>

    #sunsetdogs {
    width: 100%;
    height: auto;
}

    /* .background-image-profile {
    background-image: linear-gradient(rgba(0, 0, 0, 0.5), rgba(0, 0, 0, 0.5)), url("/data/img/sunsetdogs.jpg");
    background-position: center;
    background-repeat: repeat;
    background-size: cover;
    position: relative;
} */

    .image-profile-text {
        width: 100%;
        height: 100%;
        text-align: center;
        position: absolute;
        top: 25%;
        /* left: 50%;
        transform: translate(-50%, -50%);*/
        color: white; 
        background-repeat: repeat-y;
}
    .playdates-button{
        border-radius: 4px;
        padding: 1.5%;
        background-color: rgb(207, 207, 207);
        color: black;
        width: 30%;
        margin: 2% 2% 2% 2%;    
        font-size: 1vw;
        border-style: outset;
        box-sizing: border-box;
        
        
    }
    .playdates-button:hover {
        box-shadow: 0 12px 16px 0 rgb(105, 105, 105), 0 17px 50px 0 rgb(63, 63, 63);
    }
    h1{
        text-align: center;
    }
    a{
        text-decoration: none;
        color: black;    
    }
    /* text "My Pet Family!" */
    #new-font {
        text-align: center;
        font-size: 2vw;
        
    }
    /* whole page grid */
    #profile {
        display: grid;
        grid-template-columns: 12% 30% 16% 30% 12%;
        grid-template-areas: ". pet . playdate .";
    }
    #pets-parent {
        grid-area: pet;
        margin-bottom: 10px;
    }
    #playdates-parent {
        grid-area: playdate;
    }
    /* text "My Playdates" and host, date, address text on playdate cards */
    #playdates {
        text-align: right;
    }
    /* playdate card container */
    .card {
        border: 1px solid black;
        margin: 8px 8px 8px 8px;
        padding: 0 0 8px 0;
        border-radius: 15px;
        width: 100%;
        float: center;
        background-color: #ffe6e6;
        color: black;
        
    }
    /* playdate card grid */
    .playdate {
        display: grid;
        margin: 4px 0 4px 0;
        grid-template-columns: 2fr 10fr 1fr 10fr 2fr;
        grid-template-areas:
            ". username . time ."
            "space space space space space"
            ". address . . .";
    }
    /* host on playdate cards */
    .playdate #user {
        display: inline-block;
        text-align: left;
        font-weight: bold;
        grid-area: username;
        font-size: 1vw;
    }
    /* time on playdate cards */
    .playdate #time {
        display: inline-block;
        font-style: italic;
        grid-area: time;
        font-size: 1vw;
    }
    /* address on playdate cards */
    .playdate #address {
        text-align: left;
        display: inline-block;
        grid-area: address;
        font-size: 1vw;
    }
    .playdate #space {
        padding: 8px;
        grid-area: space;
        
    }
     /* container for buttons on playdate cards */
    #buttons {
        margin: 5px 8px 5px 8px;
        text-align: right;
        
    }
    .playdateRequestsLink {
        display: block;
    }

    /* "Register a New Pet!" button and "Create Playdate!" button */
    #red-button {
        float: center;
        padding: 2%;
        border-radius: 12px;
        background-image: linear-gradient(to bottom right, rgb(252, 71, 71), rgb(252, 153, 153));
        font-size: 1.2vw;
        color: black;
        transition-duration: 0.2s;
        width: 50%;
    }

    .smaller {
        font-size: smaller;
        text-decoration: underline;
    }
    /* Hover on "Register a New Pet!" button and "Create Playdate!" button */
    #red-button:hover { 
        background-color: rgb(252, 115, 115); 
        color: white;
    }
    /* text "My Pet Family" */
    /* h2 {
        float: center;    */
    /* } */
    /* texts "Welcome {{username}}", "My Pet Family", "My Playdates" */
    /* .new-font {
        font-size: 3vw;
    } */
    #forms {
        display: block;
    }

    /* pink card with pet family */
    #pet-card{
        border: 1px solid black;
        box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
        width: 100%;
        height: auto;
        margin: auto;
        text-align: center;
        /* font-family: arial; */
        float: left;
        background-color: #ffe6e6;
        padding: 10px 10px 10px 10px;
        border-radius: 12px; 
        color: black; 
        font-size: .5vw;
    }
    /* "You Have New Playdate Requests" button */
     #requests-button{
        float:center;
        padding: 2%;
        border-radius: 12px;
        background-image: linear-gradient(to bottom right, rgb(252, 71, 71), rgb(252, 153, 153));
        font-size: 1.2vw;
        color: black;
        transition-duration: 0.2s;
        border-width: 5px;
        border-style: dotted;
        width: 80%;
        margin-bottom: 8px;
    }

    #requests-button:hover {
        color: white;
    }

    /* All of borderBlink is part of one blinking function */
    @-webkit-keyframes borderBlink {    
    from, to {    
        border-color: transparent    
    }    
    50% {    
        border-color: white    
    }    
    }    
    @keyframes borderBlink {    
    from, to {    
        border-color: transparent    
    }    
    50% {    
        border-color: white    
    }    
}    
    .borderBlink{    
    border:1px solid white;  
    -webkit-animation: borderBlink 1s step-end infinite;    
    animation: borderBlink 1s step-end infinite;    
}
    
</style>