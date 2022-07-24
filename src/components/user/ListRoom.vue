<template>
    <div class="wrapper to" id="wrapper">
        <!-- Slide bar -->
        <NavBar/>

        <!-- Page content -->
        <div id="content">
            <div class="top-navbar">
                <Header/>
            </div>

            <!-- Main content -->
            <div class="main-content">
                <h3 style="margin:20px 0px;">
                    <i class="material-icons">
                        arrow_circle_right
                    </i>
                    Đăng ký phòng
                </h3>
                <div id="user-list-room_wrapperroom">
                <table id="user-list-room" class="table table-bordered" style="width:100%">
                    <thead>
                        <tr>
                            <th>#</th>
                            <th>Tên Phòng</th>
                            <th>Đã đăng kí</th>
                            <th>Đối tượng phòng kí túc xá</th>
                            <th>Giá phòng trên tháng</th>
                            <th>Đăng kí</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="room in this.rooms" :key="room.id" class="text-center">
                            <td>{{room.id}}</td>
                            <td>{{room.room_name}}</td>
                            <td>{{room.booked_number}} / {{room.max_number}} ({{room.type_room}})</td>
                            <td>{{room.note}}</td>
                            <td>{{room.month_cost}}</td>
                            <td>
                                <span v-if="this.isBooked === 1" class="btn btn-danger">Bạn không thể đăng kí thêm</span>
                                <p v-else-if="this.isBooked === 2" style="color: blue">Bạn đã có phòng</p>
                                <button v-else class="btn btn-primary btn-action" @click="bookingRoom(room.id)">
                                    <i class="material-icons">check</i>
                                    Đăng kí
                                </button>
                                
                            </td>
                        </tr>
                    </tbody>
                </table>
                </div>
                <!-- Footer -->
                <div class="footer-style">
                    <Footer/>
                </div>
            </div>
        </div>
    </div>
  
</template>

<script src="">
import Header from '../Header.vue'
import NavBar from '../NavBar.vue'
import Footer from '../Footer.vue'
import $ from 'jquery'
import axios from 'axios'
export default {
    name: 'ListRoom',
    components: {
        Header,
        NavBar,
        Footer
    },
    props: {
        isBooked: Number
    },
    data() {
        return {
            rooms: [],
        }
    },
    methods: {
        bookingRoom(roomId) {
            var token = this.$store.state.token
            console.log(token)
            axios.get('/api/auth', {
                headers: {
                    'Authorization': `Bearer ${token}`
                }
            }).then(resp => {
                var userId = resp.data.user.id
                const formData = {
                    user: userId,
                    room: roomId
                }
                axios.post('/api/booking-room/', formData, {
                    headers: {
                    'Authorization': `Bearer ${token}`
                    }
                }).then(resp => {
                    console.log("Dang ki thanh cong")
                    this.$store.commit('setBookingRoom', resp.data)
                    this.$router.push('/user/booking-room')
                }).catch(err => console.log(err))
            }).catch(err => console.log(err))
        }
    },
    mounted() {
        $('#user-list-room').DataTable({
            "lengthMenu": [[5, 10, 15, -1], [5, 10, 15, "All"]]
        }); 
    },
    created() {
        //Kiểm tra xem User này đã đăng kí phòng chưa
        var token = this.$store.state.token

        axios.get('/api/room/', {
            headers: {
                'Authorization': `Bearer ${token}`,
                'Content-Type': 'application/json'
            }
        })
        .then(resp => {
            console.log(resp);
            this.$store.commit('setRooms', resp.data)
        }).catch(err => console.log(err))
        this.rooms = this.$store.state.rooms
    }
}
</script>

<style scoped >
    #user-list-room_wrapperroom{
        margin-top: 20px;
        margin-bottom: 20px;
        box-shadow: 0 0 20px rgb(0 0 0 / 11%);
        background-color: #ffffff;
        padding: 30px;
        border-radius: 5px;
    }
    #user-list-room {
        padding: 30px 0px;
    }
    .btn-action {
        width: 150px;
        border-radius: 50px;
        border: none;
        background: rgb(123, 97, 226);
    }
    .main-content {
        background-color: #f2f3f8;
    }
    table.table-bordered.dataTable th, table.table-bordered.dataTable td{
        font-size: 12px;
        text-align: left;
        vertical-align: middle;
    }
    table.table-bordered.dataTable th{
        font-family: "Helvetica Neue", Helvetica, "Noto Sans", sans-serif, Arial, sans-serif;
        font-weight: 550; 
    }
    .btn-info{
        background-color: #42A5F5 !important;
        color: #ffffff;
        border-radius: 5px;
    }
    h3 {
        font-size: 22px;
    }
    .material-icons {
        font-family: 'Material Icons';
        font-weight: normal;
        font-style: normal;
        font-size: 20px !important;  /* Preferred icon size */
        display: inline-block;
        line-height: 1;
        text-transform: none;
        letter-spacing: normal;
        word-wrap: normal;
        white-space: nowrap;
        direction: ltr;

        /* Support for all WebKit browsers. */
        -webkit-font-smoothing: antialiased;
        /* Support for Safari and Chrome. */
        text-rendering: optimizeLegibility;

        /* Support for Firefox. */
        -moz-osx-font-smoothing: grayscale;

        /* Support for IE. */
        font-feature-settings: 'liga';
        }

        
</style>