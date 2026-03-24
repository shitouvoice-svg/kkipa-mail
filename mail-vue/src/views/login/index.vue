<template>
  <div id="login-box" v-loading="oauthLoading" element-loading-text="登录中...">
    
    <div class="split-left" :style="background">
      <div id="background-wrap" v-if="!settingStore.settings.background">
        <div class="x1 cloud"></div>
        <div class="x2 cloud"></div> 
        <div class="x3 cloud"></div>
        <div class="x4 cloud"></div>
        <div class="x5 cloud"></div>
      </div>
      
      <div class="illustration-container">
        <svg viewBox="0 0 400 400" width="100%" height="100%" overflow="visible">
          
          <rect x="100" y="110" width="90" height="240" fill="#6c38ff" />
          <g class="eyes purple-eyes">
            <template v-if="!isPwdFocused">
              <circle cx="115" cy="130" r="4.5" fill="#fff" />
              <circle cx="116.5" cy="130" r="2" fill="#000" />
              <circle cx="138" cy="125" r="4" fill="#fff" />
              <circle cx="139.5" cy="125" r="1.5" fill="#000" />
              <line x1="120" y1="140" x2="128" y2="140" stroke="#000" stroke-width="1.5" stroke-linecap="round"/>
            </template>
            <template v-else>
              <path d="M 111 130 Q 115 126 119 130" stroke="#000" stroke-width="2" fill="none" stroke-linecap="round"/>
              <path d="M 135 125 Q 138 122 141 125" stroke="#000" stroke-width="2" fill="none" stroke-linecap="round"/>
              <path d="M 120 138 Q 124 144 128 138" stroke="#000" stroke-width="1.5" fill="none" stroke-linecap="round"/>
            </template>
          </g>

          <rect x="165" y="165" width="55" height="185" fill="#222" />
          <g class="eyes black-eyes">
            <template v-if="!isPwdFocused">
              <circle cx="175" cy="180" r="4.5" fill="#fff" />
              <circle cx="176.5" cy="180" r="2" fill="#000" />
              <circle cx="188" cy="183" r="4.5" fill="#fff" />
              <circle cx="189.5" cy="183" r="2" fill="#000" />
            </template>
            <template v-else>
               <path d="M 171 180 Q 175 176 179 180" stroke="#fff" stroke-width="2" fill="none" stroke-linecap="round"/>
               <path d="M 184 183 Q 188 179 192 183" stroke="#fff" stroke-width="2" fill="none" stroke-linecap="round"/>
               <path d="M 176 188 Q 180 194 184 188" stroke="#fff" stroke-width="1.5" fill="none" stroke-linecap="round"/>
            </template>
          </g>

          <path d="M 205 350 L 205 240 A 32 32 0 0 1 269 240 L 269 350 Z" fill="#e8c300" />
          <g class="eyes yellow-eyes">
            <template v-if="!isPwdFocused">
              <circle cx="233" cy="225" r="3" fill="#000" />
              <line x1="205" y1="238" x2="235" y2="238" stroke="#000" stroke-width="3" stroke-linecap="round"/>
            </template>
            <template v-else>
              <line x1="230" y1="225" x2="236" y2="225" stroke="#000" stroke-width="2" stroke-linecap="round"/>
              <path d="M 210 233 Q 220 243 230 233" stroke="#000" stroke-width="2" fill="none" stroke-linecap="round"/>
            </template>
          </g>

          <path 
            :d="isEmailFocused ? 'M 40 350 A 75 75 0 0 1 115 275 L 330 275 A 75 75 0 0 1 405 350 Z' : 'M 40 350 A 75 75 0 0 1 115 275 L 115 275 A 75 75 0 0 1 190 350 Z'" 
            fill="#ff8a33" 
            style="transition: d 0.4s cubic-bezier(0.25, 0.8, 0.25, 1), all 0.4s ease;" 
          />
          <g class="eyes orange-eyes" :class="{ 'blinking-eyes': isEmailFocused }" :style="{ transform: isEmailFocused ? 'translateX(215px)' : 'none', transition: 'transform 0.4s cubic-bezier(0.25, 0.8, 0.25, 1)' }">
            <template v-if="!isPwdFocused">
              <circle cx="85" cy="300" r="4" fill="#000" />
              <circle cx="120" cy="305" r="4" fill="#000" />
            </template>
            <template v-else>
              <line x1="81" y1="300" x2="89" y2="300" stroke="#000" stroke-width="2" stroke-linecap="round"/>
              <line x1="116" y1="305" x2="124" y2="305" stroke="#000" stroke-width="2" stroke-linecap="round"/>
            </template>
            <path d="M 95 315 Q 105 325 115 315" stroke="#000" stroke-width="3" fill="none" stroke-linecap="round"/>
          </g>

        </svg>
      </div>
    </div>

    <div class="split-right form-wrapper">
      <a class="home-link" href="https://mail.xxxx.us.kg" title="Home">
        <Icon icon="tabler:home-filled" width="18" height="18" />
        <span>Home</span>
      </a>

      <div class="container">
        <div class="logo-wrap">
          <svg width="45" height="45" viewBox="0 0 24 24" fill="#1a1a1a" xmlns="http://www.w3.org/2000/svg">
            <path d="M12 3L1 9L12 15L21 10.09V17H23V9L12 3ZM5 13.18V17.18C5 18.5 8.13 20 12 20C15.87 20 19 18.5 19 17.18V13.18L12 17L5 13.18Z" />
          </svg>
        </div>
        
        <h1 class="form-title">{{ settingStore.settings.title || 'Welcome back!' }}</h1>
        
        <div class="interactive-hint" :class="{ 'is-pwd': isPwdFocused, 'is-email': isEmailFocused }">
          {{ hintText }}
        </div>

        <div v-show="show === 'login'" class="form-content">
          <div class="input-group">
            <label>Email</label>
            <el-input :class="settingStore.settings.loginDomain === 0 ? 'email-input' : ''" v-model="form.email"
                      type="text" :placeholder="$t('emailAccount')" autocomplete="off"
                      @focus="setFocus('email')" @blur="clearFocus">
              <template #append v-if="settingStore.settings.loginDomain === 0">
                <div @click.stop="openSelect" class="append-wrap">
                  <el-select
                      v-if="show === 'login'"
                      ref="mySelect"
                      v-model="suffix"
                      :placeholder="$t('select')"
                      class="select"
                  >
                    <el-option v-for="item in domainList" :key="item" :label="item" :value="item" />
                  </el-select>
                  <div class="suffix-display">
                    <span>{{ suffix }}</span>
                    <Icon class="setting-icon" icon="mingcute:down-small-fill" width="20" height="20"/>
                  </div>
                </div>
              </template>
            </el-input>
          </div>

          <div class="input-group">
            <label>Password</label>
            <el-input 
              v-model="form.password" 
              :placeholder="$t('password')" 
              type="password" 
              autocomplete="off"
              @focus="setFocus('pwd')"
              @blur="clearFocus"
            ></el-input>
          </div>

          <div class="form-options">
            <el-checkbox>Remember for 30 days</el-checkbox>
          </div>

          <el-button class="btn btn-black" type="primary" @click="submit" :loading="loginLoading">
            {{ $t('loginBtn') || 'Log In' }}
          </el-button>

          <el-button class="btn btn-linuxdo" v-if="settingStore.settings.linuxdoSwitch" style="margin-top: 15px" @click="linuxDoLogin">
            <el-avatar src="/image/linuxdo.webp" :size="18" style="margin-right: 10px" /> Continue with LinuxDo
          </el-button>
        </div>

        <div v-show="show !== 'login'" class="form-content">
          <div class="input-group">
            <label>Email Prefix</label>
            <el-input class="email-input" v-model="registerForm.email" type="text" :placeholder="$t('emailAccount')" autocomplete="off"
                      @focus="setFocus('email')" @blur="clearFocus">
              <template #append>
                <div @click.stop="openSelect" class="append-wrap">
                  <el-select
                      v-if="show !== 'login'"
                      ref="mySelect"
                      v-model="suffix"
                      :placeholder="$t('select')"
                      class="select"
                  >
                    <el-option v-for="item in domainList" :key="item" :label="item" :value="item" />
                  </el-select>
                  <div class="suffix-display">
                    <span>{{ suffix }}</span>
                    <Icon class="setting-icon" icon="mingcute:down-small-fill" width="20" height="20"/>
                  </div>
                </div>
              </template>
            </el-input>
          </div>

          <div class="input-group">
            <label>Password</label>
            <el-input 
              v-model="registerForm.password" 
              :placeholder="$t('password')" 
              type="password" 
              autocomplete="off"
              @focus="setFocus('pwd')"
              @blur="clearFocus"
            />
          </div>

          <div class="input-group">
            <label>Confirm Password</label>
            <el-input 
              v-model="registerForm.confirmPassword" 
              :placeholder="$t('confirmPwd')" 
              type="password"
              autocomplete="off"
              @focus="setFocus('pwd')"
              @blur="clearFocus"
            />
          </div>

          <el-input v-if="settingStore.settings.regKey === 0" v-model="registerForm.code" :placeholder="$t('regKey')" type="text" autocomplete="off" class="mb-3" @focus="setFocus('code')" @blur="clearFocus"/>
          <el-input v-if="settingStore.settings.regKey === 2" v-model="registerForm.code" :placeholder="$t('regKeyOptional')" type="text" autocomplete="off" class="mb-3" @focus="setFocus('code')" @blur="clearFocus"/>
          
          <div v-show="verifyShow" class="register-turnstile"
               :data-sitekey="settingStore.settings.siteKey"
               data-callback="onTurnstileSuccess"
               data-error-callback="onTurnstileError"
               data-after-interactive-callback="loadAfter"
               data-before-interactive-callback="loadBefore">
            <span style="font-size: 12px;color: #F56C6C" v-if="botJsError">{{ $t('verifyModuleFailed') }}</span>
          </div>

          <el-button class="btn btn-black" style="margin: 0" type="primary" @click="submitRegister" :loading="registerLoading">
            {{ $t('regBtn') || 'Sign Up' }}
          </el-button>
          
          <el-button v-if="settingStore.settings.linuxdoSwitch" class="btn btn-linuxdo" style="margin-top: 15px" @click="linuxDoLogin">
            <el-avatar src="/image/linuxdo.webp" :size="18" style="margin-right: 10px" /> Continue with LinuxDo
          </el-button>
        </div>

        <template v-if="settingStore.settings.register === 0">
          <div class="switch" @click="show = 'register'" v-if="show === 'login'">
            Don't have an account? <span>Sign Up</span>
          </div>
          <div class="switch" @click="show = 'login'" v-else>
            Already have an account? <span>Log In</span>
          </div>
        </template>
      </div>
    </div>

    <el-dialog class="bind-dialog" v-model="showBindForm" title="注册邮箱" >
      <div class="bind-container">
        <el-input v-model="bindForm.email" type="text" :placeholder="$t('emailAccount')" autocomplete="off">
          <template #append>
            <div @click.stop="openSelect" class="append-wrap">
              <el-select ref="mySelect" v-model="suffix" :placeholder="$t('select')" class="select">
                <el-option v-for="item in domainList" :key="item" :label="item" :value="item" />
              </el-select>
              <div class="suffix-display">
                <span>{{ suffix }}</span>
                <Icon class="setting-icon" icon="mingcute:down-small-fill" width="20" height="20"/>
              </div>
            </div>
          </template>
        </el-input>
        <el-input v-if="settingStore.settings.regKey === 0" v-model="bindForm.code" :placeholder="$t('regKey')" type="text" autocomplete="off"/>
        <el-input v-if="settingStore.settings.regKey === 2" v-model="bindForm.code" :placeholder="$t('regKeyOptional')" type="text" autocomplete="off"/>
        <el-button class="btn btn-black" type="primary" @click="bind" :loading="bindLoading">绑定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script setup>
import router from "@/router";
import {computed, nextTick, reactive, ref} from "vue";
import {login, register} from "@/request/login.js";
import {isEmail} from "@/utils/verify-utils.js";
import {useSettingStore} from "@/store/setting.js";
import {useAccountStore} from "@/store/account.js";
import {useUserStore} from "@/store/user.js";
import {useUiStore} from "@/store/ui.js";
import {Icon} from "@iconify/vue";
import {cvtR2Url} from "@/utils/convert.js";
import {loginUserInfo} from "@/request/my.js";
import {permsToRouter} from "@/perm/perm.js";
import {useI18n} from "vue-i18n";
import {oauthBindUser, oauthLinuxDoLogin} from "@/request/ouath.js";

const {t} = useI18n();
const accountStore = useAccountStore();
const userStore = useUserStore();
const uiStore = useUiStore();
const settingStore = useSettingStore();

const isPwdFocused = ref(false);
const isEmailFocused = ref(false); 
const currentFocus = ref(''); // 记录当前聚焦点：'email', 'pwd', 'code'

// 集中管理焦点状态的方法
const setFocus = (type) => {
  currentFocus.value = type;
  if (type === 'pwd') {
    isPwdFocused.value = true;
    isEmailFocused.value = false;
  } else {
    // 点击 email 和 code 都会触发橙色怪兽的互动
    isPwdFocused.value = false;
    isEmailFocused.value = true;
  }
};

const clearFocus = () => {
  currentFocus.value = '';
  isPwdFocused.value = false;
  isEmailFocused.value = false;
};

// 根据当前页面(登录/注册)和焦点状态，计算出动态互动的提示文案
const hintText = computed(() => {
  if (show.value === 'login') {
    if (isPwdFocused.value) return "输入密码中... 放心，我们会闭上眼睛不偷看的 🙈";
    if (isEmailFocused.value) return "正在输入账号... 我们在旁边看着你哦 👀";
    return "Hi，欢迎回来！请输入您的账号和密码进行登录 ✨";
  } else {
    if (currentFocus.value === 'code') return "最后一步啦！填入你的专属注册码，开启新世界的大门 🗝️";
    if (isPwdFocused.value) return "密码要牢记哦，设置一个只有你自己知道的秘密吧 🙈";
    if (isEmailFocused.value) return "想要一个什么样的高级邮箱前缀呢？想一个特别的吧 💡";
    return "欢迎加入！请先获取注册码，然后依次设置前缀、密码并填入注册码即可！ 🚀";
  }
});

const loginLoading = ref(false)
const bindLoading = ref(false)
const oauthLoading = ref(false);
const showBindForm = ref(false);
const show = ref('login')

const bindForm = reactive({
  email: '',
  oauthUserId: '',
  code: ''
})

const form = reactive({
  email: '',
  password: '',
});

const mySelect = ref()
const suffix = ref('')
const registerForm = reactive({
  email: '',
  password: '',
  confirmPassword: '',
  code: null
})

const domainList = settingStore.domainList;
const registerLoading = ref(false)
suffix.value = domainList[0]
const verifyShow = ref(false)
let verifyToken = ''
let turnstileId = null
let botJsError = ref(false)
let verifyErrorCount = 0

window.onTurnstileSuccess = (token) => {
  verifyToken = token;
};

window.onTurnstileError = (e) => {
  if (verifyErrorCount >= 4) return;
  verifyErrorCount++
  console.warn('人机验加载失败', e)
  setTimeout(() => {
    nextTick(() => {
      if (!turnstileId) {
        turnstileId = window.turnstile.render('.register-turnstile')
      } else {
        window.turnstile.reset(turnstileId);
      }
    })
  }, 1500)
};

window.loadAfter = (e) => { console.log('loadAfter') }
window.loadBefore = (e) => { console.log('loadBefore') }

const loginOpacity = computed(() => {
  const opacity = settingStore.settings.loginOpacity
  return uiStore.dark ? `rgba(20, 20, 20, ${opacity})` : `rgba(255, 255, 255, ${opacity})`
})

const background = computed(() => {
  return settingStore.settings.background ? {
    'background-image': `url(${cvtR2Url(settingStore.settings.background)})`,
    'background-repeat': 'no-repeat',
    'background-size': 'cover',
    'background-position': 'center'
  } : ''
})

const openSelect = () => {
  mySelect.value.toggleMenu()
}

function linuxDoLogin() {
  const clientId = settingStore.settings.linuxdoClientId
  const redirectUri = encodeURIComponent(settingStore.settings.linuxdoCallbackUrl)
  window.location.href = `https://connect.linux.do/oauth2/authorize?client_id=${clientId}&redirect_uri=${redirectUri}&response_type=code&scope=openid+profile+email`
}

linuxDoGetUser();

async function linuxDoGetUser() {
  const params = new URLSearchParams(window.location.search)
  const code = params.get('code')

  if (code) {
    oauthLoading.value = true
    oauthLinuxDoLogin(code).then(data => {
      bindForm.oauthUserId = data.userInfo.oauthUserId;
      if (!data.token) {
        showBindForm.value = true
        oauthLoading.value = false
        ElMessage({ message: '请注册绑定一个邮箱', type: 'warning', duration: 4000, plain: true })
        return;
      }
      saveToken(data.token);
    }).catch(() => {
      oauthLoading.value = false
    })
  }

  const cleanUrl = window.location.origin + window.location.pathname
  window.history.replaceState({}, '', cleanUrl)
}

function bind() {
  if (!bindForm.email) {
    ElMessage({ message: t('emptyEmailMsg'), type: 'error', plain: true })
    return
  }
  if (bindForm.email.length < settingStore.settings.minEmailPrefix) {
    ElMessage({ message: t('minEmailPrefix', {msg: settingStore.settings.minEmailPrefix}), type: 'error', plain: true })
    return
  }

  let email = bindForm.email + suffix.value;

  if (!isEmail(email)) {
    ElMessage({ message: t('notEmailMsg'), type: 'error', plain: true })
    return
  }

  if (settingStore.settings.regKey === 0) {
    if (!bindForm.code) {
      ElMessage({ message: t('emptyRegKeyMsg'), type: 'error', plain: true })
      return
    }
  }

  const form = {email: bindForm.email + suffix.value, oauthUserId: bindForm.oauthUserId, code: bindForm.code}
  bindLoading.value = true
  oauthBindUser(form).then(data => {
    saveToken(data.token)
  }).catch(() => {
    bindLoading.value = false
  })
}

const submit = () => {
  if (!form.email) {
    ElMessage({ message: t('emptyEmailMsg'), type: 'error', plain: true })
    return
  }
  let email = form.email + (settingStore.settings.loginDomain === 0 ? suffix.value : '');
  if (!isEmail(email)) {
    ElMessage({ message: t('notEmailMsg'), type: 'error', plain: true })
    return
  }
  if (!form.password) {
    ElMessage({ message: t('emptyPwdMsg'), type: 'error', plain: true })
    return
  }

  loginLoading.value = true
  login(email, form.password).then(async data => {
    await saveToken(data.token)
  }).finally(() => {
    loginLoading.value = false
  })
}

async function saveToken(token) {
  localStorage.setItem('token', token)
  const user = await loginUserInfo();
  accountStore.currentAccountId = user.accountId;
  userStore.user = user;
  const routers = permsToRouter(user.permKeys);
  routers.forEach(routerData => {
    router.addRoute('layout', routerData);
  });
  await router.replace({name: 'layout'})
  uiStore.showNotice()
  oauthLoading.value = false;
  bindLoading.value = false;
}

function submitRegister() {
  if (!registerForm.email) {
    ElMessage({ message: t('emptyEmailMsg'), type: 'error', plain: true })
    return
  }
  if (registerForm.email.length < settingStore.settings.minEmailPrefix) {
    ElMessage({ message: t('minEmailPrefix', {msg: settingStore.settings.minEmailPrefix}), type: 'error', plain: true })
    return
  }
  if (!isEmail(registerForm.email + suffix.value)) {
    ElMessage({ message: t('notEmailMsg'), type: 'error', plain: true })
    return
  }
  if (!registerForm.password) {
    ElMessage({ message: t('emptyPwdMsg'), type: 'error', plain: true })
    return
  }
  if (registerForm.password.length < 6) {
    ElMessage({ message: t('pwdLengthMsg'), type: 'error', plain: true })
    return
  }
  if (registerForm.password !== registerForm.confirmPassword) {
    ElMessage({ message: t('confirmPwdFailMsg'), type: 'error', plain: true })
    return
  }
  if (settingStore.settings.regKey === 0) {
    if (!registerForm.code) {
      ElMessage({ message: t('emptyRegKeyMsg'), type: 'error', plain: true })
      return
    }
  }

  if (!verifyToken && (settingStore.settings.registerVerify === 0 || (settingStore.settings.registerVerify === 2 && settingStore.settings.regVerifyOpen))) {
    if (!verifyShow.value) {
      verifyShow.value = true
      nextTick(() => {
        if (!turnstileId) {
          try {
            turnstileId = window.turnstile.render('.register-turnstile')
          } catch (e) {
            botJsError.value = true
          }
        } else {
          window.turnstile.reset('.register-turnstile')
        }
      })
    } else if (!botJsError.value) {
      ElMessage({ message: t('botVerifyMsg'), type: "error", plain: true })
    }
    return;
  }

  registerLoading.value = true

  const form = {
    email: registerForm.email + suffix.value,
    password: registerForm.password,
    token: verifyToken,
    code: registerForm.code
  }

  register(form).then(({regVerifyOpen}) => {
    show.value = 'login'
    registerForm.email = ''
    registerForm.password = ''
    registerForm.confirmPassword = ''
    registerForm.code = ''
    registerLoading.value = false
    verifyToken = ''
    settingStore.settings.regVerifyOpen = regVerifyOpen
    verifyShow.value = false
    ElMessage({ message: t('regSuccessMsg'), type: 'success', plain: true })
  }).catch(res => {
    registerLoading.value = false
    if (res.code === 400) {
      verifyToken = ''
      settingStore.settings.regVerifyOpen = true
      if (turnstileId) {
        window.turnstile.reset(turnstileId)
      } else {
        nextTick(() => { turnstileId = window.turnstile.render('.register-turnstile') })
      }
      verifyShow.value = true
    }
  });
}
</script>

<style>
.el-select-dropdown__item { padding: 0 15px; }
.no-autofill-pwd .el-input__inner { -webkit-text-security: disc !important; }
</style>

<style lang="scss" scoped>
#login-box {
  display: flex;
  width: 100vw;
  height: 100vh;
  margin: 0;
  padding: 0;
  overflow: hidden;
  font-family: 'Helvetica Neue', Arial, sans-serif;
  background: var(--el-bg-color);
}

.split-left {
  flex: 1;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #f0f2f5; 
  overflow: hidden;
  
  @media (max-width: 850px) {
    display: none;
  }
}

.illustration-container {
  width: 500px;
  height: 500px;
  z-index: 10;
  
  svg {
    transition: all 0.3s ease;
  }
  
  .eyes path, .eyes line {
    transition: all 0.2s ease;
  }
}

/* 橙色怪兽专用的眨眼动画 */
.blinking-eyes circle {
  transform-box: fill-box;
  transform-origin: center;
  animation: blink 2.5s infinite;
}

@keyframes blink {
  0%, 90%, 100% { transform: scaleY(1); }
  95% { transform: scaleY(0.1); }
}

.split-right {
  width: 500px;
  position: relative; 
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background: v-bind(loginOpacity);
  box-shadow: -10px 0 30px rgba(0, 0, 0, 0.05);
  z-index: 20;

  @media (max-width: 850px) {
    width: 100%;
    box-shadow: none;
  }
}

/* 互动温馨提示框的样式 */
.interactive-hint {
  font-size: 13px;
  color: #555;
  background: #f5f7fa;
  border: 1px solid #e4e7ed;
  border-radius: 8px;
  padding: 10px 16px;
  margin-bottom: 25px;
  text-align: center;
  line-height: 1.5;
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
  transform-origin: top center;

  /* 被激活变色，增加趣味性 */
  &.is-email {
    background: rgba(255, 138, 51, 0.08); /* 配合橙色怪兽 */
    border-color: rgba(255, 138, 51, 0.3);
    color: #e67300;
  }
  
  &.is-pwd {
    background: rgba(0, 0, 0, 0.03); /* 配合黑色/闭眼状态 */
    border-color: rgba(0, 0, 0, 0.1);
    color: #333;
  }
}

.home-link {
  position: absolute;
  top: 30px;
  right: 30px;
  display: flex;
  align-items: center;
  gap: 6px;
  color: var(--el-text-color-regular);
  text-decoration: none;
  font-size: 14px;
  font-weight: 500;
  padding: 8px 12px;
  border-radius: 20px;
  background: rgba(0, 0, 0, 0.03);
  transition: all 0.3s ease;
  z-index: 100;

  &:hover {
    color: var(--el-text-color-primary);
    background: rgba(0, 0, 0, 0.06);
  }
}

.container {
  width: 100%;
  max-width: 380px;
  padding: 40px;
  display: flex;
  flex-direction: column;

  @media (max-width: 480px) {
    padding: 20px;
  }
}

.logo-wrap {
  margin-bottom: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.form-title {
  font-size: 28px;
  font-weight: 700;
  text-align: center;
  color: var(--el-text-color-primary);
  margin-bottom: 20px;
}

.input-group {
  margin-bottom: 20px;

  label {
    display: block;
    font-size: 13px;
    font-weight: 600;
    margin-bottom: 8px;
    color: var(--el-text-color-regular);
  }

  :deep(.el-input__wrapper) {
    box-shadow: 0 1px 0 0 var(--el-border-color) inset, 0 -1px 0 0 var(--el-border-color) inset, 1px 0 0 0 var(--el-border-color) inset, -1px 0 0 0 var(--el-border-color) inset;
    padding: 4px 12px;
    border-radius: 8px;
    background: transparent;
    transition: all 0.2s ease;
    
    &.is-focus {
      box-shadow: 0 0 0 1px #1a1a1a inset !important;
    }
  }
  
  :deep(.el-input__inner) {
    height: 38px;
  }
}

.form-options {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  margin-bottom: 24px;
  font-size: 13px;
}

.btn {
  height: 44px;
  width: 100%;
  border-radius: 22px;
  font-size: 15px;
  font-weight: 600;
  border: none;
  transition: all 0.3s;
}

.btn-black {
  background-color: #1a1a1a !important;
  color: #fff !important;
  
  &:hover {
    background-color: #333 !important;
    transform: translateY(-1px);
    box-shadow: 0 4px 12px rgba(0,0,0,0.15);
  }
}

.btn-linuxdo {
  background-color: #f5f5f5 !important;
  color: #333 !important;
  border: 1px solid #ddd;
  
  &:hover {
    background-color: #eaeaea !important;
  }
}

.switch {
  margin-top: 30px;
  text-align: center;
  font-size: 14px;
  color: #666;

  span {
    color: #1890ff; 
    font-weight: 600;
    cursor: pointer;
    margin-left: 4px;
    transition: color 0.3s ease;
    
    &:hover {
      color: #40a9ff;
      text-decoration: underline;
    }
  }
}

.append-wrap {
  display: flex;
  align-items: center;
  cursor: pointer;
  padding: 0 10px;
  height: 100%;
}

.suffix-display {
  display: flex;
  align-items: center;
  color: var(--el-text-color-primary);
  font-weight: 500;
}

:deep(.el-input-group__append) {
  padding: 0 !important;
  background: transparent;
  border-radius: 0 8px 8px 0;
  border-left: none;
}

.select {
  position: absolute;
  right: 30px;
  width: 100px;
  opacity: 0;
  pointer-events: none;
}

.mb-3 { margin-bottom: 20px; }
.register-turnstile { margin-bottom: 20px; }

#background-wrap {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 0;
  opacity: 0.6;
}

@keyframes animateCloud {
  0% { margin-left: -500px; }
  100% { margin-left: 100%; }
}

.x1 { animation: animateCloud 30s linear infinite; transform: scale(0.65); }
.x2 { animation: animateCloud 15s linear infinite; transform: scale(0.3); }
.x3 { animation: animateCloud 25s linear infinite; transform: scale(0.5); }
.x4 { animation: animateCloud 13s linear infinite; transform: scale(0.4); }
.x5 { animation: animateCloud 20s linear infinite; transform: scale(0.55); }

.cloud {
  background: linear-gradient(to bottom, #fff 5%, #f1f1f1 100%);
  border-radius: 100px;
  box-shadow: 0 8px 5px rgba(0, 0, 0, 0.05);
  height: 120px;
  width: 350px;
  position: relative;
  margin-top: 50px;
}

.cloud:after, .cloud:before {
  content: "";
  position: absolute;
  background: #fff;
  z-index: -1;
}

.cloud:after {
  border-radius: 100px;
  height: 100px;
  left: 50px;
  top: -50px;
  width: 100px;
}

.cloud:before {
  border-radius: 200px;
  height: 180px;
  width: 180px;
  right: 50px;
  top: -90px;
}

:deep(.bind-dialog) {
  width: 400px !important;
  border-radius: 12px;
  @media (max-width: 440px) {
    width: calc(100% - 40px) !important;
    margin: 0 20px !important;
  }
}

.bind-container {
  display: grid;
  grid-template-columns: 1fr;
  gap: 15px;
}
</style>
