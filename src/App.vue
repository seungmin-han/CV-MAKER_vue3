<template>
	<button @click="editMode=!editMode" style="position:fixed; top: 100px; left:100px; width:100px; height:50px">{{editMode?'View':'Edit'}} Mode</button>
	<div class="container">
		<h1>이 력 서</h1>
		<!-- 프로필 -->
		<div class="profile_container mt-50">
			<div class="profile_img" @click="fileEl.click()">
				<img :src="profile.imgSrc" width="125" height="" alt="프로필 이미지">
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
						<td colspan="3"><input type="text" v-model="profile.dob"></td>
					</tr>
					<tr>
						<th>성별</th>
						<td><input type="tel" v-model="profile.gender"></td>
						<th>전화번호</th>
						<td><input type="text" v-model="profile.phoneNumber"></td>
					</tr>
					<tr>
						<th>이메일</th>
						<td><input type="email" v-model="profile.email"></td>
						<th>Github</th>
						<td><input type="url" v-model="profile.githubAddress"></td>
					</tr>
					<tr>
						<th>주소</th>
						<td colspan="3"><textarea rows="1" v-model="profile.address" @input="resize"></textarea></td>
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
						<th v-if="editMode"></th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="(education,index) in educationList" :key="index">
						<td><input type="text" v-model="education.eduDate"></td>
						<td><input type="text" v-model="education.eduSchoolNm"></td>
						<td><input type="text" v-model="education.eduMajor"></td>
						<td>
							<select v-if="editMode" name="graduationCode" v-model="education.graduationCode">
								<option v-for="option in OPTION_LIST" :key="option.value" :value="option.value">
									{{option.title}}
								</option>
							</select>
							<template v-else>
								{{education.graduationCode == -1 ? '' : OPTION_LIST.find(v=>v.value==education.graduationCode)?.title}} 
							</template>
						</td>
						<td v-if="editMode">
							<button 
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
						<th v-if="editMode"></th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="(career,index) in careerList" :key="index">
						<td><input type="text" v-model="career.careerDate"></td>
						<td><input type="text" v-model="career.careerCompany"></td>
						<td><input type="text" v-model="career.careerWork"></td>
						<td v-if="editMode">
							<button 
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
						<th v-if="editMode"></th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="(award,index) in awardList" :key="index">
						<td><input type="text" v-model="award.awardDate"></td>
						<td><input type="text" v-model="award.awardNm"></td>
						<!-- <td><input type="text" maxlength="50" v-model="award.awardLevel"></td> -->
						<td><textarea v-model="award.awardLevel" maxlength="50" rows="1" @input="resize"></textarea></td>
						<td><input type="text" v-model="award.awardInstitution"></td>
						<td v-if="editMode">
							<button 
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
						<th v-if="editMode"></th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="(license,index) in licenseList" :key="index">
						<td><input type="text" v-model="license.licenseDate"></td>
						<td><input type="text" v-model="license.licenseNm"></td>
						<td><input type="text" v-model="license.licenseInstitution"></td>
						<td v-if="editMode">
							<button 
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
		<!-- 포트폴리오 -->
		<div class="portfolio_container table_container mt-50">
			<h3>포트폴리오</h3>
			<table>
				<thead>
					<tr>
						<th>기간</th>
						<th>활동명</th>
						<th>활동내용</th>
						<th v-if="editMode"></th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="(portfolio, index) in portfolioList" :key="index">
						<td><input type="text" v-model="portfolio.portfolioDate"></td>
						<td><input type="text" v-model="portfolio.portfolioNm"></td>
						<!-- <td><input type="text" v-model="portfolio.portfolioContent"></td> -->
						<td><textarea v-model="portfolio.portfolioContent" rows="1" @input="resize"></textarea></td>
						<td v-if="editMode">
							<button 
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

	const OPTION_LIST = [
		{value:'-1',title:'졸업 구분을 선택해주세요'},
		{value:'0',title:'재학'},
		{value:'1',title:'졸업'},
		{value:'2',title:'휴학'},
		{value:'3',title:'중퇴'},
	]

	const fileEl = ref(null);
	const profile = reactive({});
	const careerList = reactive([{}]);
	const educationList = reactive([{graduationCode:-1}]);
	const awardList = reactive([{}]);
	const licenseList = reactive([{}]);
	const portfolioList = reactive([{}]);

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
		portfolio: () => {
			portfolioList.push({});
		},
	}

	watch(editMode, (newValue)=>{
		let els = document.querySelectorAll('input, select, textarea');
		for (let el of els) {
			el.disabled = !newValue;
		}
	});

	onMounted(()=> {
		let els = document.querySelectorAll('input, select, textarea');
		for (let el of els) {
			el.disabled = true;
		}
	})

	const resize = (e) => {
		e.target.style.height = '1px';
		e.target.style.height = e.target.scrollHeight + 'px';
	}

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
		padding-left: 5px;
		background-color: #ddd;
	}

	td {
		text-align: center;
		input, textarea {
			padding-left: 5px;
		}
		> input {
			width: 100%;
			line-height: inherit;
			text-align: center;
			word-wrap: break-word;
		}
		>textarea {
			width: 100%;
			height: auto;
			outline: none;
			border: none;
			resize: none;
		}
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
		box-shadow: 0px 0px 20px #000;

		width: 21cm;
		min-height: 29.7cm;
		padding: 20px;
		
		margin: 0 auto;
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
				width: 125px;
				height: 150px;
				line-height: 150px;
				font-size: 12px;
				text-align: center;
				display: flex;
				align-content: center;
			}
		}

		.table_container {
			width: 92%;
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
	
	body {
		margin: 30px 0;
	}

</style>
