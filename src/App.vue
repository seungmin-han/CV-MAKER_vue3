<template>
	<div class="container">
		<h1>이 력 서 양 식</h1>
		<div class="profile_container mt-50">
			<div class="profile_img" @click="fileEl.click()">
				<img :src="profile.imgSrc" width="100" height="" alt="프로필 이미지">
				<input type="file" style="display: none;" ref="fileEl" @change="changeImg">
			</div>
			<div class="profile_info">
				<table>
					<tr>
						<th>이름</th>
						<td><input type="text" v-model="profile.krNm"></td>
						<th>영문 이름</th>
						<td><input type="text" v-model="profile.engNm"></td>
					</tr>
					<tr>
						<th>주민등록번호</th>
						<td><input type="text" v-model="profile.dob"></td>
						<th>성별</th>
						<td><input type="text" v-model="profile.gender"></td>
					</tr>
					<tr>
						<th>전화번호</th>
						<td><input type="text" v-model="profile.phoneNumber"></td>
						<th>이메일</th>
						<td><input type="email" v-model="profile.email"></td>
					</tr>
				</table>
			</div>
		</div>
		<div class="education_container mt-50">
			<h3>학력</h3>
			<table>
				<thead>
					<tr>
						<th>재학기간</th>
						<th>기관명</th>
						<th>전공</th>
						<th>졸업구분</th>
						<th></th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="(education,index) in educationList" :key="index">
						<td><input type="text" v-model="education.eduDate"></td>
						<td><input type="text" v-model="education.eduSchoolNm"></td>
						<td><input type="text" v-model="education.eduMajor"></td>
						<td>
							<select name="graduationCode" v-model="education.graduationCode">
								<option value="-1">졸업 구분을 선택해주세요</option>
								<option value="0">재학</option>
								<option value="1">졸업</option>
								<option value="2">휴학</option>
								<option value="3">중퇴</option>
							</select>
						</td>
						<td>
							<button class="btn delete" @click="educationList.splice(index,1)">
								삭제
							</button>
						</td>
					</tr>
					<tr>
						<td colspan="5">
							<button class="btn" @click="addEducationList">
								추가
							</button>
						</td>
					</tr>
				</tbody>
			</table>
			
		</div>
		<div class="career_container mt-50">
			<h3>경력</h3>
			<table>
				<thead>
					<tr>
						<th>근무기간</th>
						<th>소속</th>
						<th colspan="2">직무</th>
						<th></th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="(career,index) in careerList" :key="index">
						<td><input type="text" v-model="career.careerDate"></td>
						<td><input type="text" v-model="career.careerCompany"></td>
						<td colspan="2"><input type="text" v-model="career.careerWork"></td>
						<td>
							<button class="btn delete" @click="careerList.splice(index,1)">
								삭제
							</button>
						</td>
					</tr>
					<tr>
						<td colspan="5">
							<button class="btn" @click="addCareerList">
								추가
							</button>
						</td>
					</tr>
				</tbody>
			</table>
			
		</div>
	</div>
</template>

<script setup>
import { reactive, ref } from "vue";
	const fileEl = ref(null);
	const profile = reactive({});
	const careerList = reactive([{}]);
	const addCareerList = () => careerList.push({});
	const educationList = reactive([{graduationCode:-1}]);
	const addEducationList = () => educationList.push({graduationCode:-1});
	const changeImg = (e)=> {
		console.log(e);
		try {
			let reader = new FileReader();
			reader.onload = (res) => {
				profile.imgSrc = res.target.result;
			}
			reader.readAsDataURL(e.target.files[0]);
		} catch (err) {
			console.error(err);
		}
	}
</script>
<style lang="scss">
	* {
		margin: 0;
		padding: 0;
		list-style: none;
		box-sizing: border-box;
		text-decoration: none;
	}

	.mt-50 {
		margin-top: 50px;
	}

	table, td, th {
		border-collapse : collapse;
		border: 1px solid;
	}

	table {
		width: 100%;
	}

	th {
		background-color: #ddd;
	}

	td > input {
		width: 100%;
		line-height: inherit;
	}

	input, select {
		border: none;
	}
	input:focus {
		outline: none;
	}
	

	.container {
		margin: 0 auto;
		width: 1200px;
		display: flex;
		flex-direction: column;
		align-items: center;
		.profile_container {
			width: 100%;
			display: flex;
			align-items: center;
			justify-content: center;

			> * {
				margin: 0 30px;	
			}	
			th {
				padding: 5px;
				text-align: left;
			}

			.profile_img {
				box-sizing: content-box;
				border: 1px solid #000;
				width: 100px;
				height: 120px;
				line-height: 120px;
				font-size: 12px;
				text-align: center;
				display: flex;
				align-content: center;
			}
		}

		.education_container, .career_container {
			width: 60%;
			table {
				margin-top: 20px;
			}
			.btn {
				padding: 5px;
				width: 100%;
				font-size: 12px;
				font-weight: bold;
				border: none;
				&.delete {
					background-color: #f94141;
					color: #fff;
				}
			}
			
		}
	}
	

</style>
