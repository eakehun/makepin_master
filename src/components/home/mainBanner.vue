<template>
	<div class="swiper_test" md="12">
		<swiper ref="mySwiper" :options="swiperOptions">
			<swiper-slide>
				<div><div @click="giftCardBuy"><img alt="Vue logo" src="@/assets/make_pin_88.jpg"></div></div>
			</swiper-slide>
			<swiper-slide>
				<div><div @click="giftCardBuy"><img alt="Vue logo" src="@/assets/make_pin_88.jpg"></div></div>
			</swiper-slide>
			<div class="swiper-pagination" slot="pagination"></div>
		</swiper>
	</div>
</template>

<script>
	import axios from "axios"
	import { mapActions, mapState } from "vuex"
	import 'swiper/swiper-bundle.css'
	export default {
		name: 'mainBanner',
		data() {
			return {
				swiperOptions: {
					loop: true,
					spaceBetween: 0,
					autoplay: {
						delay: 5500,
						disableOnInteraction: true
					},
					pagination: {
						el: '.swiper-pagination',
					},
				}
			}
		},
		
		computed: {
			...mapState(['isLogin', 'userInfor', 'deviceType']),

			swiper() {
				return this.$refs.mySwiper.$swiper
			}
		},

		mounted() {
            //console.log('Current Swiper instance object', this.swiper)
            //this.swiper.slideTo(3, 1000, false)
        },

        methods: {
        	...mapActions(["logOut"]),

        	giftCardBuy(){
                if(this.isLogin == false){
                    this.showMsgBoxTwo('로그인이 필요한 서비스입니다.\n로그인 페이지로 이동하시겠습니까?.', 1)
                } else {
                    this.checkAccountCode()
                }
            },

            checkAccountCode(){
                axios
                .get(process.env.VUE_APP_BASE_URL+'/users/info/', { withCredentials: true })
                .then( res => {
                    if(res.data.accountCode != null){
                        this.$router.push({name: 'giftCardInput', path: '/giftCardBuying'});
                    } else {
                        this.showMsgBoxTwo('계좌인증이 필요합니다.\n계좌인증을 위해 마이페이지로 이동합니다.', 2)
                    }
                })
                .catch( err => {
                    // console.log(err);
                });
            },

            showMsgBoxTwo(text, type) {
                var contText = ''
                var text_c = text.split('\n')
                this.boxTwo = ''
                const h = this.$createElement
                const messageVNode = h('div', { class: ['foobar'] }, [
                    h('p', { class: ['mt-3'] }, [
                        text_c[0],
                        h('br', 'msgBoxConfirm'),
                        text_c[1],
                        ]),
                    ])

                if(type >= 1){
                    contText = [messageVNode]
                } else {
                    contText = text
                }

                this.$bvModal.msgBoxConfirm(contText, {
                    okTitle: '확인',
                    cancelTitle: '취소',
                    okVariant: 'outline-dark',
                    cancelVariant: 'outline-dark',
                    footerClass: 'p-2 border-top-0',
                    scrollable: true,
                    hideHeaderClose: false,
                    centered: true,
                    noFade: false,
                })
                .then(value => {
                    this.boxTwo = value
                    if(type == 1){
                        if(value == true){
                            this.$router.push({name: 'logInFrom', path: '/logIn', query: {type: 2}}, function() {
                                //console.log("logIn 호출 완료");
                            });
                        }
                    } else if(type == 2){
                        this.$router.push({name: 'privacyManageMent', path: '/myPage/privacyManageMent'}, function() {
                            //console.log("privacyManageMent 호출 완료");
                        });
                    } else {
                        if(value == true) this.logOut()
                    }
                })
                .catch(err => {
                    // console.log(err)
                })
            },

        }
    }

</script>

<style scoped="">
.swiper_test{width:100%;background-color:#f9f3d0;}
.swiper-container{width:100%;height:100%}
.swiper-slide{text-align:center;font-size:18px;background-color:#4d7ce0;display:-webkit-box;display:-ms-flexbox;display:-webkit-flex;display:flex;-webkit-box-pack:center;-ms-flex-pack:center;-webkit-justify-content:center;justify-content:center;-webkit-box-align:center;-ms-flex-align:center;-webkit-align-items:center;align-items:center}
.swiper-slide div{height:450px;}
.swiper-slide div img{height:100%;}

.mobile .swiper-slide div{height:auto;}
.mobile .swiper-slide div img{width:100%;}

</style>