<template>
    <div class="login-container">

        <el-form ref="loginForm" :model="loginForm" :rules="loginRules" class="login-form" auto-complete="on" label-position="left"
                 v-loading="loading" element-loading-background="rgba(0, 0, 0, 0.2)" >

            <div class="title-container">
                <h3 class="title">{{ $t('system.title') }}</h3>
                <lang-select class="set-language"/>
            </div>

            <el-form-item prop="username">
                <span class="svg-container"><svg-icon icon-class="user"/></span>
                <el-input v-model="loginForm.username" name="username" type="text" auto-complete="on" />
            </el-form-item>

            <el-form-item prop="password">
                <span class="svg-container"><svg-icon icon-class="password"/></span>
                <el-input :type="passwordType" v-model="loginForm.password" name="password" auto-complete="on" @keyup.enter.native="handleLogin"/>
                <span class="show-pwd" @click="showPwd"><svg-icon icon-class="eye"/></span>
            </el-form-item>

            <el-button type="primary" :loading="loading" style="width:100%;margin-bottom:30px;" @click.native.prevent="handleLogin">
                {{ $t('system.logIn') }}
            </el-button>
        </el-form>
    </div>
</template>

<script>
	import LangSelect from '@/components/LangSelect'

	export default {
		name: 'Login',
		components: {LangSelect},
		data() {
			return {
				loginForm: {
					username: '',
					password: ''
				},
				loginRules: {
					username: [{required: true, trigger: 'blur'}],
					password: [
						{required: true, trigger: 'blur'},
						{type: 'string',min:5, trigger: 'blur'}
                    ]
				},
				passwordType: 'password',
				loading: false,
				redirect: undefined
			}
		},
		watch: {
			$route: {
				handler: function (route) {
					this.redirect = route.query && route.query.redirect
				},
				immediate: true
			}

		},
		methods: {
			showPwd() {
				if (this.passwordType === 'password') {
					this.passwordType = ''
				} else {
					this.passwordType = 'password'
				}
			},
			handleLogin() {
				this.$refs.loginForm.validate(valid => {
					if (valid) {
						this.loading = true
						this.$store.dispatch('LoginByUsername', this.loginForm).then(() => {
                            this.$message.success('Login Success!');
							this.loading = false
							this.$router.push({path: this.redirect || '/'})
						}).catch(() => {
							this.loading = false
                            this.$message.error('Login Error!');
						})
					} else {
						console.log('error submit!!')
						return false
					}
				})
			}
		}
	}
</script>

<style rel="stylesheet/scss" lang="scss">
    $bg: #283443;
    $light_gray: #eee;
    $cursor: #fff;

    @supports (-webkit-mask: none) and (not (cater-color: $cursor)) {
        .login-container .el-input input {
            color: $cursor;
            &::first-line {
                color: $light_gray;
            }
        }
    }

    /* reset element-ui css */
    .login-container {
        .el-input {
            display: inline-block;
            height: 47px;
            width: 85%;
            input {
                background: transparent;
                border: 0px;
                -webkit-appearance: none;
                border-radius: 0px;
                padding: 12px 5px 12px 15px;
                color: $light_gray;
                height: 47px;
                caret-color: $cursor;
                &:-webkit-autofill {
                    -webkit-box-shadow: 0 0 0px 1000px $bg inset !important;
                    -webkit-text-fill-color: $cursor !important;
                }
            }
        }
        .el-form-item {
            border: 1px solid rgba(255, 255, 255, 0.1);
            background: rgba(0, 0, 0, 0.1);
            border-radius: 5px;
            color: #454545;
        }
    }
</style>

<style rel="stylesheet/scss" lang="scss" scoped>
    $bg: #2d3a4b;
    $dark_gray: #889aa4;
    $light_gray: #eee;

    .login-container {
        position: fixed;
        height: 100%;
        width: 100%;
        background-color: $bg;
        .login-form {
            position: absolute;
            left: 0;
            right: 0;
            width: 520px;
            max-width: 100%;
            padding: 35px 35px 15px 35px;
            margin: 120px auto;
        }
        .tips {
            font-size: 14px;
            color: #fff;
            margin-bottom: 10px;
            span {
                &:first-of-type {
                    margin-right: 16px;
                }
            }
        }
        .svg-container {
            padding: 6px 5px 6px 15px;
            color: $dark_gray;
            vertical-align: middle;
            width: 30px;
            display: inline-block;
        }
        .title-container {
            position: relative;
            .title {
                font-size: 26px;
                color: $light_gray;
                margin: 0px auto 40px auto;
                text-align: center;
                font-weight: bold;
            }
            .set-language {
                color: #fff;
                position: absolute;
                top: 5px;
                right: 0px;
            }
        }
        .show-pwd {
            position: absolute;
            right: 10px;
            top: 7px;
            font-size: 16px;
            color: $dark_gray;
            cursor: pointer;
            user-select: none;
        }
    }
</style>
