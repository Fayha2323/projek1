<template>
  <div class="friends-main-container">
    <div class="side-links">
<div class="list-group-button">

      
        <div  @click="showFriends">
          Friends
          <span>{{ userData.friends.length }}</span>
        </div>

        <div  @click="showFollowing">
          Following <span>{{ userData.following.length }}</span>
        </div>
        <div  @click="showFollowers">
          Followers
          <span>{{ userData.followers.length }}</span>
        </div>
     
</div>
    </div>

    <div class="friends-container">

      <div class="friends-container-header">
        <h4>Friends </h4>
      </div>
      <div class="friends-main-container">
      <!-- <ul v-show="displayFriends" v-for="user in userData.friends" :key="user.userName" class="friend-content"
        @click="showUserProfile(user.userName, user.userId)"> -->
      <!-- 
          <div class="user-images">
            <img src="https://themified.com/friend-finder/images/covers/1.jpg" alt="" class="cover-image">

            <img :src="handleImages('bose')" alt="" class="userIcon-image">

          </div>
          <div class="user-name-details">

            {{ user.userName }}

          </div> -->
      <!-- <li>yes</li>



      </ul> -->



      <div v-show="displayFriends" v-for="user in $store.state.userData.friends" :key="user.userName"
        class="friend-content">

        <div class="user-images" @click="showUserProfile(user.userName, user.userId)">
          <img src="https://themified.com/friend-finder/images/covers/1.jpg" alt="" class="cover-image">

          <img :src="handleImages(user.userName)" alt="" class="userIcon-image">

        </div>


        <div class="user-name-details" @click="showUserProfile(user.userName, user.userId)">
          <div>
            <h5>{{ user.userName }}</h5>
            <font-awesome-icon :icon="['fas', 'user-friends']" />
          </div>
        </div>
        <button class="btn btn-danger">Unfriend</button>
      </div>

<div v-show="displayFollowers" v-for="user in $store.state.userData.followers" :key="user.userName"
        class="friend-content">

        <div class="user-images" @click="showUserProfile(user.userName, user.userId)">
          <img src="https://themified.com/friend-finder/images/covers/1.jpg" alt="" class="cover-image">

          <img :src="handleImages(user.userName)" alt="" class="userIcon-image">

        </div>


        <div class="user-name-details" @click="showUserProfile(user.userName, user.userId)">
          <div>
            <h5>{{ user.userName }}</h5>
            <font-awesome-icon :icon="['fas', 'user-friends']" />
          </div>
        </div>
        <button class="btn btn-danger">Unfollow</button>
      </div>

<div v-show="displayFollowing" v-for="user in $store.state.userData.following" :key="user.userName"
        class="friend-content">

        <div class="user-images" @click="showUserProfile(user.userName, user.userId)">
          <img src="https://themified.com/friend-finder/images/covers/1.jpg" alt="" class="cover-image">

          <img :src="handleImages(user.userName)" alt="" class="userIcon-image">

        </div>


        <div class="user-name-details" @click="showUserProfile(user.userName, user.userId)">
          <div>
            <h5>{{ user.userName }}</h5>
            <font-awesome-icon :icon="['fas', 'user-friends']" />
          </div>
        </div>
        <button class="btn btn-danger">Follow back</button>
      </div>


</div>
    </div>


































    <div class="people-container">

      <div class="people-header">
        <h4>People you May know</h4>
      </div>

      <div class="people-main-container">


        <div v-for="user in randomUsers" :key="user.userName" class="friends-contents">
          <div @click="showUserProfile(user.userName)" class="friends-contents-list">
            <div class="userName-image">
              <img :src="handleImages(user.userName)" alt="">
              <span>
                <h5>{{ user.userName }}</h5>
              </span>

            </div>
            <div class="request-buttons">
              <button class="btn btn-info" @click="handleFriendRequest(user,user.requestStatus)">
                {{ user.requestStatus }}
              </button>

              <button class="btn btn-info" v-if="user.requestStatus === 'Friend Request Sent'"
                @click="handleCancelFriendRequest(user, user.userName )">
                Cancel request
              </button>
              <button class="btn btn-info" v-if="!followingList.includes(user.userName)"
                @click="handleFollow(user.userName,'follow')">follow</button>
              <button class="btn btn-info" v-if="followingList.includes(user.userName)"
                @click="handleFollow(user.userName,'unfollow')">unfollow</button>
            </div>

          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import { uuid } from 'vue-uuid';



  export default {
    name: "Friends",
    props: ["userName"],

    data() {
      return {
        userData: {
          userName: "Guest",
          userId: "419",
          emailAddress: "419",
          firstName: "Guest",
          lastName: "Guest",
          address: "Guest",
          emailAddress: "Guest@gmail.com",
          postCode: "Guest",
          country: "Guest",
          city: "Guest",
          aboutMe: "Guest",
          password: "Guest",
          occupation: "Guest",
          education: "Guest",
          age: "27",
          messages: [],
          posts: {
            Guest: { likes: ["Guest"], unLikes: ["Guest"] },
          },
          followers: ["Guest"],
          following: ["Guest"],
          friends: ["Guest"],

          posterComment: "",
          comments: {
            likes: [],
            unLikes: [],
          },
        },
        allUsers: [],
        displayFriends: true,
        displayFollowers: false,
        displayFollowing: false,
        followingList: []
      };
    },
    mounted() {
      this.loadData();
    },
    beforeUnmount() {
      this.loadData();
    },
    methods: {
      loadData() {

        if (this.$store.state.userData.userName === "Guest") {
          this.$router.push({
            name: 'Login',
          });



        }

        this.userData = this.$store.state.userData;

        const followingList = this.userData.following
        this.followingList = followingList.map((userName) => userName.userName)


        this.$store.dispatch("handleNotificationUpdate", {
          userName: this.userData.userName,
          notificationStatus: "Read",
          notificationType: 'friend request'
        });



        const users = Object.keys(this.$store.state.allUsers);
        const allUsers = this.$store.state.allUsers;
        const friendsList = this.userData.friends.map((user) => user.userName)
        const randomUsers = users.filter((userName) => ![...friendsList, this.userData.userName].includes(userName))

        for (const userName in allUsers) {
          for (let index in allUsers[userName].requests) {
            if (allUsers[userName].requests[index].userName === this.userData.userName) {
              allUsers[userName].requestStatus = "Friend Request Sent";
            }
            else {
              allUsers[userName].requestStatus = "Add Friend";

            }

          }
        }

        for (let index in allUsers[this.userData.userName].requests) {
          if (allUsers[this.userData.userName].requests.length) {
            allUsers[allUsers[this.userData.userName].requests[index].userName].requestStatus = "Accept Request"
          }


        }
        this.allUsers = allUsers;
        console.log(this.userData.friends);

      },

      toggleDisplay(display) {
        switch (display) {
          case "displayFriends":
            this.displayFriends = true;
            this.displayFollowers = false;
            this.displayFollowing = false;

            break;

          case "displayFollowers":
            this.displayFriends = false;
            this.displayFollowers = true;
            this.displayFollowing = false;
            break;

          case "displayFollowing":
            this.displayFriends = false;
            this.displayFollowers = false;
            this.displayFollowing = true;
            break;

          default:
            break;
        }
      },

      handleImages(userName) {
        if (this.$store.state.users[userName].userThumbnail !== undefined && userName !== undefined && this.$store.state.users[userName].userThumbnail.length) {
          return this.$store.state.users[userName].userThumbnail

        }

        return "https://d1nhio0ox7pgb.cloudfront.net/_img/o_collection_png/green_dark_grey/512x512/plain/user.png"
      },

      showFriends() {
        if (this.userData.userName !== "Guest") {
          this.toggleDisplay("displayFriends");
          this.$router.push({
            name: "Friends",
            params: { userName: this.userData.userName },
          });
        }
      },
      showFollowers() {
        if (this.userData.userName !== "Guest") {
          this.toggleDisplay("displayFollowers");
        }
      },
      showFollowing() {
        if (this.userData.userName !== "Guest") {
          this.toggleDisplay("displayFollowing");
        }
      },


      showUserProfile(userName) {
        if (this.userData.userName !== "Guest") {
          this.$router.push({
            name: "Timeline",
            params: { userName, },
          });
        }

      },


      handleFollow(userName, followState) {
        this.$store.dispatch("handleFollow", {
          friendUserName: userName,
          userName: this.userData.userName,
          followState
        });

        let followingList = this.userData.following

        this.followingList = followingList.map((userName) => userName.userName)

        this.$store.dispatch("handleNotifications", {
          userName: this.userData.userName,
          friendUserName: userName,
          notificationId: uuid.v1(),
          notificationType: 'follow',
          notificationStatus: "unRead",
          notificationDate: Date.now(),

        });
        if (followState === "follow") {
          this.$store.dispatch("handleActivities", {
            friendUserName: userName,
            userName: this.userData.userName,
            activity: "followed",
            activityDate: Date.now(),
            activityId: uuid.v1()
          });



        }

      },



      handleFriendRequest(user, requestStatus) {
        switch (requestStatus) {
          case "Add Friend":

            user.requestStatus = "Friend Request Sent";

            this.$store.dispatch("handleFriendRequest", {
              friendUserName: user.userName,
              userName: this.userData.userName,
              requestStatus: "Friend Request Sent",
            });

            this.$store.dispatch("handleNotifications", {
              userName: this.userData.userName,
              friendUserName: user.userName,
              notificationId: uuid.v1(),
              notificationType: 'friend request',
              notificationStatus: "unRead",
              notificationDate: Date.now(),

            });

            this.$store.dispatch("handleActivities", {
              userName: this.userData.userName,
              friendUserName: user.userName,
              activity: "Friend request",
              activityDate: Date.now(),
              activityId: uuid.v1()
            });

            break;

          case "Accept Request":
            this.$store.dispatch("handleFriendRequest", {
              friendUserName: user.userName,
              userName: this.userData.userName,
              requestStatus: "Accept Request",
            });

            this.$store.dispatch("handleActivities", {
              friendUserName: user.userName,
              userName: this.userData.userName,
              activity: "Accepted Friend Request",
              activityDate: Date.now(),
              activityId: uuid.v1()
            });

            this.$store.dispatch("handleNotifications", {
              userName: this.userData.userName,
              friendUserName: user.userName,
              notificationId: uuid.v1(),
              notificationType: "Accepted Friend Request",
              notificationStatus: "unRead",
              notificationDate: Date.now(),

            });

            break;

          default:
            break;
        }


      },

      handleCancelFriendRequest(user, friendUserName) {


        this.$store.dispatch("handleCancelFriendRequest", {
          userName: friendUserName,
          friendUserName: this.userData.userName
        }
        )
        this.loadData()

      },

    },

    computed: {
      randomUsers() {
        const friends = this.userData.friends.map((friend) => friend.userName);
        let allUsers = []
        for (const user in this.$store.state.allUsers) {
          allUsers = [...allUsers, this.$store.state.allUsers[user]]

        }

        // this.userData = this.$store.state.userData;

        const randomUsers = allUsers
          .filter(
            (user) =>
              ![this.userData.userName, ...friends].includes(user.userName)
          )
          .map((a) => ({ sort: Math.random(), value: a }))
          .sort((a, b) => a.sort - b.sort)
          .map((a) => a.value);


        return randomUsers;
      },
    },
  };
</script>




















<style scoped>

</style>