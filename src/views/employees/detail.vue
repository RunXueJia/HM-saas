<template>
	<div class="dashboard-container">
		<div class="app-container">
			<el-card>
				<el-tabs>
					<el-tab-pane label="登录账户设置">
						<!-- 放置表单 -->
						<el-form :model="formData" label-width="120px" style="margin-left: 120px; margin-top:30px">
							<el-form-item label="姓名:">
								<el-input disabled v-model="formData.username" style="width:300px" />
							</el-form-item>
							<el-form-item label="密码:">
								<el-input show-password v-model="formData.password2" style="width:300px" type="password" />
							</el-form-item>
							<el-form-item>
								<el-button @click="submit" type="primary">更新</el-button>
							</el-form-item>
						</el-form>
					</el-tab-pane>
					<el-tab-pane label="个人详情">
						<el-row type="flex" justify="end">
							<el-tooltip content="打印个人基本信息">
								<router-link :to="`/employees/print/${userId}?type=personal`">
									<i class="el-icon-printer" />
								</router-link>
							</el-tooltip>
						</el-row>
						<UserInfo @update="initData" ref="userinfo" />
					</el-tab-pane>
					<el-tab-pane label="岗位信息">
						<el-row type="flex" justify="end">
							<el-tooltip content="打印岗位信息">
								<router-link :to="`/employees/print/${userId}?type=job`">
									<i class="el-icon-printer" />
								</router-link>
							</el-tooltip>
						</el-row>
						<component :is="'JobInfo'"></component>
					</el-tab-pane>
				</el-tabs>
			</el-card>
		</div>
	</div>
</template>

<script>
	import UserInfo from "./components/user-info.vue";
	import JobInfo from "./components/job-info.vue";
	import { getUserDetailById, saveUserDetailById } from "@/api/employees";
	export default {
		name: "HRSaasDetail",
		components: { UserInfo, JobInfo },
		data() {
			return {
				userId: this.$route.params.id,
				formData: {
					password2: "",
				},
			};
		},

		mounted() {},
		created() {
			this.initData();
		},
		methods: {
			async initData() {
				const res = await getUserDetailById(this.userId);
				// console.log(res);
				this.formData = res;
				this.$refs.userinfo.userInfo = res;
				if (res.staffPhoto && res.staffPhoto.trim()) {
					this.$refs.userinfo.$refs.ygtx.fileList = [
						{
							url: res.staffPhoto,
							name: "",
							upload: true,
						},
					];
				}
				// console.log(res);
			},
			async submit() {
				try {
					await saveUserDetailById({
						...this.formData,
						password: this.formData.password2 || "",
					});
				} catch (error) {
					return;
				}
				this.$message.success("更新成功");

				this.initData();
			},
		},
	};
</script>

<style lang="scss" scoped>
</style>