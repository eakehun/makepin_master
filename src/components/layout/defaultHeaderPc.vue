<template>
    <div>
        <div class="top_banner" v-if="appTopBannerSta">
            <a href="https://play.google.com/store/apps/details?id=kr.co.make.android.makePin&hl=ko" target='_blank'><img class="top_banner2" src="@/assets/images/makepin_top2.png"></a>
            <img class="top_banner1"src="@/assets/images/makepin_top.jpg">
        </div>
        <b-container class="header_cont">
            <router-link to="/" class="logo"><img alt="Vue logo" :src="logoImg"></router-link>
            <div class="header_menu_cont">
                <div class="menu">
                    <ul>
                        <li><div class="me_menu" :class=" isActive == 1 ? 'active' : ''" @click="giftCardBuy">상품권 판매신청</div></li>
                        <li @mouseover="listOne = true" @mouseleave="listOne = false">
                            <div class="me_menu" :class=" isActive == 2 ? 'active' : ''" @click="isLoginChecked('/myPage')">마이페이지</div>
                            <transition name="fade">
                                <ul v-if="listOne" @click="listOne = false">
                                    <li><div class="me_menu" :class=" isSubActive == 1 ? 'active' : ''" @click="isLoginChecked('/myPage')">판매거래내역</div></li>
                                    <li><div class="me_menu" :class=" isSubActive == 2 ? 'active' : ''" @click="isLoginChecked('/myPage/accountManageMent')">계좌인증 관리</div></li>
                                    <li><div class="me_menu" :class=" isSubActive == 3 ? 'active' : ''" @click="isLoginChecked('/myPage/privacyManageMent')">개인정보 관리</div></li>
                                    <li><div class="me_menu" :class=" isSubActive == 4 ? 'active' : ''" @click="isLoginChecked('/myPage/giftCardSaleLimitCheck')">판매한도 관리</div></li>
                                </ul>
                            </transition>
                        </li>
                        <li @mouseover="listTwo = true" @mouseleave="listTwo = false">
                            <router-link to="/board" :class=" isActive == 3 ? 'active' : ''">고객센터</router-link>
                            <transition name="fade">
                                <ul v-if="listTwo" @click="listTwo = false">
                                    <li><router-link to="/board" :class=" isSubActive == 5 ? 'active' : ''">공지사항</router-link></li>
                                    <li><router-link to="/board/frequentlyAskedQuestions" :class=" isSubActive == 6 ? 'active' : ''">FAQ</router-link></li>
                                    <li><div class="me_menu" :class=" isSubActive == 7 ? 'active' : ''" @click="isLoginChecked('/board/userInquiry')">1:1 문의/답변</div></li>
                                </ul>
                            </transition>
                        </li>
                    </ul>
                </div>
                <div class="user_menu">
                    <div v-if="isLogin">
                        <router-link to="/myPage">{{userInfor}} 님</router-link>
                        <div class="btna" @click="showMsgBoxTwo('로그아웃 하시겠습니까?')">로그아웃</div>
                    </div>
                    <div v-else>
                        <router-link to="/logIn">로그인</router-link>
                        <router-link to="/signup">회원가입</router-link>
                    </div>
                </div>
            </div>
        </b-container>

    </div>
</template>

<script>
    import axios from "axios"
    import { mapActions, mapState } from "vuex"
    export default {
        name: 'headerPc',
        data() {
            return{
                logoImg: require('@/assets/images/make_pin_logo.png'),
                listOne:false,
                listTwo:false,
                activeMenu1: false,
                activeMenu2: false,
                isActive: 0,
                isSubActive: '',
                appTopBannerSta: true,
            }
        },
        created() {
            this.isActiveFun(this.$route.name);
            if( navigator.userAgent.indexOf('makePin') != -1){
                this.appTopBannerSta = false
            }
        },
        computed:{
            ...mapState(['isLogin', 'userInfor', 'deviceType', 'logInSuccess'])
        },

        methods: {
            ...mapActions(["logOut", "checkSession"]),

            isLoginChecked(page){
                if(this.isLogin == false){
                    this.showMsgBoxTwo('로그인이 필요한 서비스입니다.\n로그인 페이지로 이동하시겠습니까?.', 1)
                } else {
                    this.$router.push({path: page});
                }
            },

            giftCardBuy(){

                // axios
                // .get(process.env.VUE_APP_BASE_URL+'/users/info/', { withCredentials: true })
                // .then( res => {
                //     this.checkAccountCode();
                // })
                // .catch( err => {
                //     this.showMsgBoxTwo('로그인이 필요한 서비스입니다.\n로그인 페이지로 이동하시겠습니까?.', 1)
                // });

                if(this.isLogin == false){
                    this.showMsgBoxTwo('로그인이 필요한 서비스입니다.\n로그인 페이지로 이동하시겠습니까?.', 1)
                } else {
                    this.checkAccountCode();
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

            mouseOver: function(event) {
                if(event.target.className == 'coustom'){
                    this.coustomActive = !this.coustomActive;
                    this.mypageActive = false;
                } else {
                    this.mypageActive = !this.mypageActive;
                    this.coustomActive = false;
                }
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

            isActiveFun(val){
                switch (val){
                    case 'giftCardInput' :
                        this.isActive = 1
                        break;
                    case 'giftCardComplete' :
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
                    case 'withDraw' :
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
                        this.isSubActive = 0
                }
            },

            mouseOut: function() {
                // console.log('mouseOut')
            },
            mobileMenu: function(obj){
                // console.log(obj)
            }
        },
        watch: {
            $route (data){
                this.isActiveFun(this.$route.name)
            }
        }
    }
</script>

<style>
*:focus { outline:none; }
.top_banner{position:relative;height:100px;overflow:hidden;}
.top_banner1{width:100%;min-height:100px;}
.top_banner2{position:absolute;top:51%;left:50%;width:600px;margin-left:-300px;margin-top:-28px}
.defaultHeader{position:relative;z-index:20;}
.header_menu_cont:after{content:'';display:block;clear:both;}
.box{width:100px;height:100px;background-color:#ccc;}
.defaultHeader{}
.header_cont{margin:0 auto;padding:20px 0;}
.header_cont:after{content:'';display:block;clear:both;}
.logo{float:left;}
.logo img{width:180px;margin:3px 0;}

.user_menu{float:right;}
.user_menu:after{content:'';display:block;clear:both;}
.user_menu .btna{float:left;cursor:pointer;font-size:.85em;background-color:#fff;color:#777;border:none;padding:13px 10px;position:relative;}

.user_menu a{float:left;font-size:.85em;color:#777;padding:13px 10px;position:relative;}
.user_menu a:first-child:after{display:none;}
.user_menu .btna:after,
.user_menu a:after{content:'/';display:block;clear:both;font-size:1em;font-weight:400;position:absolute;top:50%;left:0;margin-top:-9px;color:#ccc;}

.menu{float:left;margin-left:50px;margin-top:7px;}
.menu > a{display:block;padding:20px;color:#444;text-decoration:none;text-align:center;}
.menu ul{margin:0;}
.menu ul:after{content:'';display:block;clear:both;}
.menu li{display:block;float:left;position:relative;color:#444;min-width:150px}
.menu li > .me_menu{cursor:pointer;}
.menu li > .me_menu,
.menu li > a{padding:7px;color:#444;font-size:1em;display:block;text-align:center;}
.menu li > .me_menu.active,
.menu li > a.active{font-weight:700;color:#2e48ab;}
.menu li ul{position:absolute;left:0;top:40px;margin:0;padding:0 0 10px 0;border-radius:10px;background:#fff;}
.menu li ul li{transition:background .2s}
.menu li ul li:hover{}
.fade-enter-active,.fade-leave-active{transition:opacity .2s}
.fade-enter,.fade-leave-active{opacity:0}

.mobile_menu{display:none;}
.mobile .mobile_menu{display:block;position:absolute;top:20px;right:10px;padding:5px;}
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
.m_menu_cont .m_menu_b a{color:#999;}
.m_footer{text-align:center;font-size:.87rem;color:#666;position:absolute;bottom:0;left:0;width:100%;padding:20px 0}

.t_menu{width:100%;text-align:center;}
.t_menu_btn{text-align:center;color:#444;padding:10px 0;}
.t_menu_ui{padding:20px}
.t_menu_ui a{font-size:.87rem;color:#999;padding:5px 0;}
.t_menu.not-collapsed{text-decoration:underline;}
.t_menu .t_menu_body{display:none;padding:15px 0;}
.t_menu.active .t_menu_body{display:block;}
.t_menu .t_menu_body a{font-size:.87em;color:#777;padding:5px 0;}
</style>