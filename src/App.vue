<template>
	<button @click="editMode=!editMode">토글</button>
	<div class="container">
		<h1>이 력 서 양 식</h1>
		<!-- 프로필 -->
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
		<!-- 학력 -->
		<div class="education_container table_container mt-50">
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
							<button 
								v-if="editMode"
								class="btn delete" 
								:disabled="index==0" 
								:class="{disabled:index==0}"
								@click="educationList.splice(index,1)">
								삭제
							</button>
						</td>
					</tr>
					<tr>
						<td v-if="editMode" colspan="5">
							<button class="btn" @click="addItem.education">
								추가
							</button>
						</td>
					</tr>
				</tbody>
			</table>
		</div>
		<!-- 경력 -->
		<div class="career_container table_container mt-50">
			<h3>경력</h3>
			<table>
				<thead>
					<tr>
						<th>근무기간</th>
						<th>소속</th>
						<th>직무</th>
						<th></th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="(career,index) in careerList" :key="index">
						<td><input type="text" v-model="career.careerDate"></td>
						<td><input type="text" v-model="career.careerCompany"></td>
						<td><input type="text" v-model="career.careerWork"></td>
						<td>
							<button 
								v-if="editMode"
								class="btn delete" 
								:disabled="index==0" 
								:class="{disabled:index==0}"
								@click="careerList.splice(index,1)">
								삭제
							</button>
						</td>
					</tr>
					<tr>
						<td v-if="editMode" colspan="4">
							<button class="btn" @click="addItem.career">
								추가
							</button>
						</td>
					</tr>
				</tbody>
			</table>
		</div>
		<!-- 수상기록 -->
		<div class="award_container table_container mt-50">
			<h3>수상기록</h3>
			<table>
				<thead>
					<tr>
						<th>수상일자</th>
						<th>상훈명</th>
						<th>수상내역</th>
						<th>수여기관</th>
						<th></th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="(award,index) in awardList" :key="index">
						<td><input type="text" v-model="award.awardDate"></td>
						<td><input type="text" v-model="award.awardNm"></td>
						<td><input type="text" v-model="award.awardLevel"></td>
						<td><input type="text" v-model="award.awardInstitution"></td>
						<td>
							<button 
								v-if="editMode"
								class="btn delete" 
								:disabled="index==0" 
								:class="{disabled:index==0}"
								@click="awardList.splice(index,1)">
								삭제
							</button>
						</td>
					</tr>
					<tr>
						<td v-if="editMode" colspan="5">
							<button class="btn" @click="addItem.award">
								추가
							</button>
						</td>
					</tr>
				</tbody>
			</table>
		</div>
		<!-- 자격증 -->
		<div class="license_container table_container mt-50">
			<h3>자격증</h3>
			<table>
				<thead>
					<tr>
						<th>취득일자</th>
						<th>자격증명</th>
						<th>발행기관</th>
						<th></th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="(license,index) in licenseList" :key="index">
						<td><input type="text" v-model="license.licenseDate"></td>
						<td><input type="text" v-model="license.licenseNm"></td>
						<td><input type="text" v-model="license.licenseInstitution"></td>
						<td>
							<button 
								v-if="editMode"
								class="btn delete" 
								:disabled="index==0" 
								:class="{disabled:index==0}"
								@click="licenseList.splice(index,1)">
								삭제
							</button>
						</td>
					</tr>
					<tr>
						<td v-if="editMode" colspan="4">
							<button class="btn" @click="addItem.license">
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
import { onMounted, reactive, ref, watch } from "vue";
	const fileEl = ref(null);
	const profile = reactive({});
	const careerList = reactive([{}]);
	const educationList = reactive([{graduationCode:-1}]);
	const awardList = reactive([{}]);
	const licenseList = reactive([{}]);

	const editMode = ref(false);

	const addItem =  {
		career: () => {
			careerList.push({});
		},
		education: () => {
			educationList.push({graduationCode:-1});
		},
		award: () => {
			awardList.push({});
		},
		license: () => {
			licenseList.push({});
		},
	}

	watch(editMode, (newValue)=>{
		let els = document.querySelectorAll('input, select');
		for (let el of els) {
			el.disabled = !newValue;
		}
	});

	onMounted(()=> {
		let els = document.querySelectorAll('input, select');
		for (let el of els) {
			el.disabled = true;
		}
	})

	const changeImg = (e)=> {
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
	button {
		cursor: pointer;
		&.disabled {
			opacity: 0.3;
			pointer-events: none;
		}
	}
	

	.container {
		width: 21cm;
		height: 29.7cm;
		
		margin: 0 auto;
		display: flex;
		flex-direction: column;
		align-items: center;
		h1 {
			margin-top: 20px;
		}
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

		.table_container {
			width: 90%;
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
