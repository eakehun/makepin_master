<template>
	<div>
		<b-container class="mt-md-0 mt-4 mb-md-0 mb-4 mypage_cont_body">
			<slot name="header"></slot>
			<div class="mypage_container">
				<div class="bv-example-row mt-md-5 mt-4 mb-md-5 mb-4">
					<h5>비밀번호 확인</h5>
					<div>안전한 서비스 이용 및 개인정보 보호를 위해 비밀번호를 입력해 주세요.</div>
					<div class="from_box">
						<input
						id="input-password"
						v-model="pwd"
						type="password"
						required
						placeholder="비밀번호를 입력해주세요."
						@keyup.enter="userStaChecked"
						>
						<button variant="primary" class="btnn bTcont grd" @click="userStaChecked">확인</button>
					</div>
				</div>
			</div>
		</b-container>
	</div>
</template>
<script>
	import { mapState, mapActions } from "vuex"
	import axios from "axios"
	export default{
		name: 'accountManageMent',
		data() {
			return {
				uesrId:'',
				pwd: '',
			}
		},
		computed:{
            ...mapState(['isLogin'])
        },
		mounted(){
			this.userData();
			if(this.isLogin){
			} else {
				// console.log('로그인이 필요한 페이지 입니다.');
				// this.logOut();
			}
		},
		methods: {
			...mapActions(["logOut"]),

			showMsgBoxOne(text, type) {
				this.boxTwo = ''
				this.$bvModal.msgBoxOk(text, {
					okTitle: '확인',
					okVariant: 'outline-dark',
					footerClass: 'p-2 border-top-0',
					hideHeaderClose: false,
					centered: true
				})
				.then(value => {
					this.boxTwo = value
					if(type == true){
						this.$router.push({name: 'accountManageMentDetail', path: 'accountManageMentDetail', query: {user: true}}, function() {
						//console.log("accountManageMentDetail 호출 완료");
					});
					} else {
						this.logOut()
					}

				})
				.catch(err => {

				})
			},

			userData(){
				axios
				.get(process.env.VUE_APP_BASE_URL+'/users/info/', { withCredentials: true })
				.then( res => {
					this.uesrId = res.data.userId
				})
				.catch( err => {
					// console.log(err);
				});
			},

			userStaChecked(){
				axios
				.post(''+process.env.VUE_APP_BASE_URL+'/users/login/', {"userId": this.uesrId,"pwd": this.pwd},{ withCredentials: true })
				.then( res => {
					this.showMsgBoxOne('확인되었습니다.', true)
				})
				.catch( err => {
					if(err.response.data.message === process.env.VUE_APP_LOGIN_MESSAGE){
						this.showMsgBoxOne('정보가 일치하지 않습니다. \n 안전한 서비스이용을 위해 로그아웃합니다.', false)
					} else {
						// console.log(2)
					}

					if(err.response.data.message == "suspended user"){
						this.showMsgBoxOne('정보가 일치하지 않습니다. \n 안전한 서비스이용을 위해 로그아웃합니다.', false)
					}
				});
			},
		},
	}

</script>

<style scoped="">
.btnn{width:100%;}
.from_box{padding:20px 0;margin-bottom:10px;}
.from_box input{width:100%;border:solid 1px #ddd;border-radius:5px;margin:1rem 0 2rem 0;padding:6px 10px;}
.btn.login_btn{width:100%;border-radius:0;}
.user_infor{text-align:center;}
.user_infor a{color:#444;font-size:.75em;}
.user_infor a:first-child:before{display:none;}
.user_infor a:first-child + a:before{content:'/';display:inline-block;clear:both;padding:0 5px;}
.sign_up_btn{float:left;width:100%;background-color:#fff;height:40px;line-height:40px;text-align:center;color:#444;margin-top:10px;border:solid 1px #ccc;}
</style>