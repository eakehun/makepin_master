<template>
    <div>
        <b-collapse id="collapse_new" v-model="appmTopBannerSta" class="mt-2">
            <b-card>
                <div class="app_cont">
                    <div class="app_cont_title">
                        <div><span>메이크핀 APP </span> 다운받기!</div>   
                        <div>※ 안드로이드만 가능합니다.</div>   
                    </div>
                    <div class="app_cont_btnbody">
                        <div>
                            <a href="https://play.google.com/store/apps/details?id=kr.co.make.android.makePin&hl=ko" target='_blank' class="app_cont_btn">앱다운받기</a>
                            <b-icon icon="x" font-scale="2" class="close_icon2" @click="appmTopBannerSta = !appmTopBannerSta"></b-icon>
                        </div>
                        <div></div>
                    </div>
                </div>   
            </b-card>
        </b-collapse>
        <div v-if="topHistory == 'historyBack'" class="sdfun">
            <b-container class="header_cont history_cont">
                <h5 class="top_htitle">상품권 판매 요청</h5>
                <b-icon icon="x" font-scale="2" class="close_icon" @click="$router.push({path: '/'})"></b-icon>
            </b-container>
        </div>
        
        <div v-else class="sdfun">
            <b-container class="header_cont mobile">
                <router-link to="/" class="logo"><img alt="Vue logo" :src="logoImg" :nametpye="this.$route.name"></router-link>
                <div>
                    <div class="mobile_menu" @click="mobile_menu_reset" v-b-toggle.sidebar-1>
                        <b-icon icon="list" font-scale="2"></b-icon>
                    </div>
                
                    <b-sidebar id="sidebar-1" ref="mySidebar" sidebar-class="w-100" no-header shadow>
                        <template v-slot:default="{ hide }">
                            <div class="px-3 py-3">
                                <div class="m_head mb-4">
                                    <router-link to="/" class="menu_logo"><img alt="Vue logo" :src="logoImg"></router-link>
                                    <div class="close_btn" block @click="hide"><b-icon icon="x" font-scale="2"></b-icon></div>
                                </div>
                                <div class="m_menu_cont px-3 py-2">
                                    <div class="m_menu_a">
                                        <div class="t_menu_btn" :class=" isActive == 1 ? 'active' : ''" @click="giftCardBuy">상품권 판매신청</div>
                                        <template >
                                            <div class="t_menu menu1" @click="clickEvent">
                                                <div class="t_menu_btn" :class=" isActive == 2 ? 'active' : ''">마이페이지</div>
                                                <div class="t_menu_body">
                                                    <div class="m_menu" :class=" isSubActive == 1 ? 'active' : ''" @click="isLoginChecked('/myPage')">판매거래내역</div>
                                                    <div class="m_menu" :class=" isSubActive == 2 ? 'active' : ''" @click="isLoginChecked('/myPage/accountManageMent')">계좌인증 관리</div>
                                                    <div class="m_menu" :class=" isSubActive == 3 ? 'active' : ''" @click="isLoginChecked('/myPage/privacyManageMent')">개인정보 관리</div>
                                                    <div class="m_menu" :class=" isSubActive == 4 ? 'active' : ''" @click="isLoginChecked('/myPage/giftCardSaleLimitCheck')">판매한도 확인</div>
                                                </div>
                                            </div>
                                        </template>

                                        <template>
                                            <div class="t_menu menu2" @click="clickEvent">
                                                <div class="t_menu_btn" :class=" isActive == 3 ? 'active' : ''">고객센터</div>
                                                <div class="t_menu_body">
                                                    <router-link to="/board" :class=" isSubActive == 5 ? 'active' : ''">공지사항</router-link>
                                                    <router-link to="/board/frequentlyAskedQuestions" :class=" isSubActive == 6 ? 'active' : ''">FAQ</router-link>
                                                    <div class="m_menu" :class=" isSubActive == 7 ? 'active' : ''" @click="isLoginChecked('/board/userInquiry')">1:1문의/답변</div>
                                                </div>
                                            </div>
                                        </template>

                                    </div>
                                    <div class="m_menu_b">
                                        <div v-if="isLogin">
                                            <router-link to="/myPage">{{userInfor}} 님</router-link>
                                            <div class="btna" @click="showMsgBoxTwo">로그아웃</div>
                                        </div>
                                        <div v-else>
                                            <router-link to="/logIn">로그인</router-link>
                                            <router-link to="/signup">회원가입</router-link>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="m_footer">Copyright © 2020 BY OOO. ALL RIGHTS RESERVED</div>
                        </template>
                    </b-sidebar>
                </div>
            </b-container>
        </div>
    </div>
</template>

<script>
    import axios from "axios"
    import { mapActions, mapState } from "vuex"
    export default {
        name: 'defaultHeaderMobile',
        props: ['reFineTarget'],
        data() {
            return{
                logoImg: require('@/assets/images/make_pin_logo.png'),
                mobileHead: this.reFineTarget,
                topHistory: '',
                activeMenu1: false,
                activeMenu2: false,
                isActive: 0,
                isSubActive: '',
                appmTopBannerSta: true
            }
        },
        computed:{
            ...mapState(['isLogin', 'userInfor', 'deviceType'])
        },
        created() {
            this.isActiveFun(this.$route.name);
            if( navigator.userAgent.indexOf('makePin') != -1){
                this.appmTopBannerSta = false
            }
        },
        methods: {
            ...mapActions(["logOut"]),

            isLoginChecked(page){
                if(this.isLogin == false){
                    this.$refs.mySidebar.hide()
                    this.showMsgBoxTwo3('로그인이 필요한 서비스입니다.\n로그인 페이지로 이동하시겠습니까?.', 1)
                } else {
                    this.$router.push({path: page});
                }
            },

            giftCardBuy(){
                if(this.isLogin == false){
                    this.showMsgBoxTwo2('로그인이 필요한 서비스입니다.\n로그인 페이지로 이동하시겠습니까?.', 1)
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
                        this.showMsgBoxTwo2('계좌인증이 필요합니다.\n계좌인증을 위해 마이페이지로 이동합니다.', 2)
                    }
                })
                .catch( err => {
                    // console.log(err);
                });
            },

            showMsgBoxTwo3(text, type) {
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
                        } else {
                            this.logOut()
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


            showMsgBoxTwo2(text, type) {
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
                            this.$router.push({name: 'logInFrom', path: '/logIn', query: {type: 2}});
                        }
                    } else if(type == 2){
                        this.$router.push({name: 'privacyManageMent', path: '/myPage/privacyManageMent'});
                    } else {
                        if(value == true) this.logOut()
                    }
                })
                .catch(err => {
                    // console.log(err)
                })
            },

            clickEvent(obj){
                var activeMenu1 = document.getElementsByClassName('menu1')[0];
                var activeMenu2 = document.getElementsByClassName('menu2')[0];
                
                if(obj.target.parentElement.className.indexOf('menu1') != -1){
                    activeMenu1.classList.add('active')
                    activeMenu2.classList.remove('active')
                } else {
                    activeMenu2.classList.add('active')
                    activeMenu1.classList.remove('active')
                }
            },

            isActiveFun(val){
                this.topHistory = ''
                switch (val){
                    case 'giftCardInput' :
                        this.topHistory = 'historyBack'
                        this.isActive = 1
                        break;
                    case 'giftCardDealHistory' :
                        this.isActive = 2
                        this.isSubActive = 1
                        break;
                    case 'giftCardDealHistoryDetail' :
                        this.isActive = 2
                        this.isSubActive = 1
                        break;
                    case 'accountManageMent' :
                        this.isActive = 2
                        this.isSubActive = 2
                        break;
                    case 'accountManageMentDetail' :
                        this.isActive = 2
                        this.isSubActive = 2
                        break;
                    case 'privacyManageMent' :
                        this.isActive = 2
                        this.isSubActive = 3
                        break;
                    case 'privacyManageMentDetail' :
                        this.isActive = 2
                        this.isSubActive = 3
                        break;
                    case 'giftCardSaleLimitCheck' :
                        this.isActive = 2
                        this.isSubActive = 4
                        break;
                    case 'noticeBoard' :
                        this.isActive = 3
                        this.isSubActive = 5
                        break
                    case 'frequentlyAskedQuestions' :
                        this.isActive = 3
                        this.isSubActive = 6
                        break
                    case 'userInquiry' :
                        this.isActive = 3
                        this.isSubActive = 7
                        break;   
                    case 'userInquiryWriting' :
                        this.isActive = 3
                        this.isSubActive = 7
                        break;       
                    default :
                        this.isActive = 0
                }
            },

            mobile_menu_reset(obj){
                document.getElementsByClassName('t_menu').forEach(obj=>obj.classList.remove("active"))
            },

            showMsgBoxTwo() {
                this.boxTwo = ''
                this.$bvModal.msgBoxConfirm('로그아웃 하시겠습니까?', {
                    okTitle: '확인',
                    cancelTitle: '취소',
                    okVariant: 'outline-dark',
                    cancelVariant: 'outline-dark',
                    footerClass: 'p-2 border-top-0',
                    hideHeaderClose: false,
                    centered: true
                })
                .then(value => {
                    this.boxTwo = value
                    if(value == true) this.logOut()
                })
                .catch(err => {
                    // console.log(err)
                })
            },
        },
        watch: {
            reFineTarget (val){
                this.mobileHead = val
            },

            $route (data){
                this.isActiveFun(this.$route.name)
            }
        }
    }
</script>

<style scoped="">
*:focus { outline:none; }
.card-body{padding:1rem;}

.sdfun{position:relative;}
.defaultHeader{position:relative;z-index:20;}
.header_menu_cont:after{content:'';display:block;clear:both;}
.box{width:100px;height:100px;background-color:#ccc;}
.defaultHeader{}
.header_cont{margin:0 auto;padding:15px 0;}
.header_cont.history_cont{border-bottom:solid 1px #ddd;}
.header_cont.history_cont .b-icon.bi{vertical-align:middle;}
.header_cont:after{content:'';display:block;clear:both;}
.logo{float:left;}
.logo img{width:180px;}
.close_icon{position:absolute;top:50%;right:10px;margin-top:-16px;}
.top_htitle{text-align:center;margin:0;}
.user_menu{float:right;}
.user_menu:after{content:'';display:block;clear:both;}
.user_menu a{float:left;font-size:.7em;color:#777;padding:13px 10px;position:relative;}
.user_menu a:first-child:after{display:none;}
.user_menu a:after{content:'/';display:block;clear:both;font-size:1em;font-weight:400;position:absolute;top:50%;left:0;margin-top:-9px;color:#ccc;}

.menu{float:left;margin-left:50px;margin-top:7px;}
.menu > a{display:block;padding:20px;color:#444;text-decoration:none;text-align:center;}
.menu ul:after{content:'';display:block;clear:both;}
.menu li{display:block;float:left;position:relative;color:#444;min-width:150px}
.menu li > a{padding:7px;color:#444;font-size:.85em;display:block;text-align:center;}
.menu li ul{position:absolute;left:0;top:40px;margin:0;padding:0 0 10px 0;border-radius:10px;background:#fff;}
.menu li ul li{transition:background .2s}
.menu li ul li:hover{}
.fade-enter-active,.fade-leave-active{transition:opacity .2s}
.fade-enter,.fade-leave-active{opacity:0}

.mobile_menu{display:none;}
.mobile .mobile_menu{display:block;position:absolute;top:20px;right:10px;padding:5px;}
.mobile .defaultHeader .header_cont{padding:20px 0;}
.mobile .defaultHeader .header_cont .logo{float:none;display:block;text-align:center;}
.mobile .defaultHeader .header_menu_cont{display:none;}

.mobile .m_head{}
.mobile .m_head:after{content:'';display:block;clear:both;}
.mobile .menu_logo{float:left;width:130px;}
.mobile .menu_logo img{width:100%;}
.close_btn{float:right;}

.m_menu_cont{}
.m_menu_a{padding:10px 0;}
.m_menu_b{padding:10px 0;border-top:solid 1px #ccc;}
.m_menu_cont a{display:block;text-align:center;color:#444;padding:10px 0;}
.m_menu_cont a.active{font-weight:700;}
.m_menu_cont .m_menu_b a{color:#999;}
.m_menu_cont .m_menu_b .btna{display:block;text-align:center;color:#444;padding:10px 0;color:#999;}
.m_footer{text-align:center;font-size:13px;color:#666;position:absolute;bottom:0;left:0;width:100%;padding:20px 0}

.t_menu{width:100%;text-align:center;}
.t_menu_btn{text-align:center;color:#444;padding:10px 0;}
.t_menu_btn.active{font-weight:700;}
.t_menu_ui{padding:20px}
.t_menu_ui a{font-size:.87rem;color:#999;padding:5px 0;}
.t_menu.not-collapsed{text-decoration:underline;}
.t_menu .t_menu_body{display:none;padding:15px 0;}
.t_menu.active .t_menu_body{display:block;}
.t_menu .t_menu_body .m_menu,
.t_menu .t_menu_body a{font-size:.87em;color:#777;padding:5px 0;}


.card{border:none;border-radius:0;border-bottom:solid 1px #ccc;position:relative;}
.app_cont_title{}
.app_cont_title > div{font-size:16px;font-weight:700;color:#444;}
.app_cont_title > div span{color:#4d7ce0;}

.app_cont_title > div + div{font-size:13px;color:#888;font-weight:400;}
.close_icon2{width:30px;height:30px;position:absolute;top:50%;right:10px;margin-top:-15px}
.app_cont_btn{padding:0 10px;height:40px;line-height:40px;border-radius:20px;border:solid 1px #4d7ce0;color:#4d7ce0;background-color:#fff;position:absolute;top:50%;right:50px;margin-top:-20px}

</style>