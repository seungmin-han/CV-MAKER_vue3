<template>
	<div>
	<div style="position:fixed; top: 0px; left:0px;">
		<button 
			@click="editMode=!editMode"
			style="width:100px; height:50px"
		>
			{{editMode?'View':'Edit'}} Mode
		</button>
		<button 
			@click="isRow=!isRow"
			style="width:100px; height:50px"
		>
			{{isRow ? 'Column' : 'Row' }} View
		</button>
		<button 
			@click="saveToPDF"
			style="width:100px; height:50px"
		>
			Save to PDF
		</button>
	</div>
	<vue-html2pdf
		class="h2p"
		:class="{row:isRow}"
		ref="h2p"
        :float-layout="false"
        :enable-download="false"
        :preview-modal="true"
        filename="이력서"
        :pdf-quality="2"
        :manual-pagination="true"
        pdf-format="a4"
	>
		<template v-slot:pdf-content>
			<div 
				class="container-wrap" 
				:class="isRow ? 'direction-row' : 'direction-col'"
			>
				<!-- 이력서 -->
				<div class="container" :class="{save:saveMode}">
					<h1>이 력 서</h1>
					<!-- 프로필 -->
					<div class="profile-container mt-50">
						<div class="profile-img" 
							:class="[{'img-selected':profile.imgSrc}, {'view-mode':!editMode}]" 
							@click="fileEl.click()"
						>
							<p v-if="editMode && !profile.imgSrc">이미지 선택</p>
							<img :src="profile.imgSrc" v-if="profile.imgSrc" width="125" height="" alt="프로필 이미지">
							<input type="file" style="display: none;" ref="fileEl" @change="changeImg">
						</div>
						<div class="profile-info">
							<table>
								<tr>
									<th>이름</th>
									<td><input type="text" v-model="profile.krNm"></td>
									<th>영문 이름</th>
									<td><input type="text" v-model="profile.engNm"></td>
								</tr>
								<tr>
									<th>주민등록번호</th>
									<td colspan="3"><input class="ta-l" type="text" v-model="profile.dob"></td>
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
									<td colspan="3" class="profile">
										<div 
											class="textarea-div ta-l profile" 
											:contenteditable="editMode" 
											@input="inputText(profile, $event, 'address')"
										> 
											{{profile.address}}
										</div>
									</td>
								</tr>
							</table>
						</div>
					</div>
					<!-- 학력 -->
					<div class="education-container table-container mt-50">
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
								<!-- <Draggable v-model="educationList" class="draggable-comp">
									<template #item="{ item }">
											<td><input type="text" v-model="item.eduDate"></td>
											<td><input type="text" v-model="item.eduSchoolNm"></td>
											<td><input type="text" v-model="item.eduMajor"></td>
											<td>
												<select v-if="editMode" v-model="item.graduationCode">
													<option v-for="option in OPTION_LIST" :key="option.value" :value="option.value">
														{{option.title}}
													</option>
												</select>
												<template v-else>
													{{item.graduationCode == -1 ? '' : OPTION_LIST.find(v=>v.value==item.graduationCode)?.title}} 
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
									</template>
								</Draggable> -->
								<tr v-for="(education,index) in educationList" :key="index">
									<td><input type="text" v-model="education.eduDate"></td>
									<td><input type="text" v-model="education.eduSchoolNm"></td>
									<td><input type="text" v-model="education.eduMajor"></td>
									<td>
										<select v-if="editMode" v-model="education.graduationCode">
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
					<div class="career-container table-container mt-50">
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
					<div class="award-container table-container mt-50">
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
									<td class="award">
										<div 
											class="textarea-div ta-l" 
											:contenteditable="editMode" 
											@input="inputText(award, $event, 'awardNm')"
										> 
											{{award.awardNm}}
										</div>
									</td>
									<td class="award">
										<div 
											class="textarea-div ta-l" 
											:contenteditable="editMode" 
											@input="inputText(award, $event, 'awardLevel')"
										> 
											{{award.awardLevel}}
										</div>
									</td>
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
					<div class="license-container table-container mt-50">
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
					<div class="portfolio-container table-container mt-50">
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
									<td class="portfolio">
										<div 
											class="textarea-div ta-l portfolio" 
											:contenteditable="editMode" 
											@input="inputText(portfolio, $event, 'portfolioContent')"
										> 
											{{portfolio.portfolioContent}}
										</div>
									</td>
									<td v-if="editMode">
										<button 
											class="btn delete" 
											:disabled="index==0" 
											:class="{disabled:index==0}"
											@click="portfolio.splice(index,1)">
											삭제
										</button>
									</td>
								</tr>
								<tr>
									<td v-if="editMode" colspan="4">
										<button class="btn" @click="addItem.portfolio">
											추가
										</button>
									</td>
								</tr>
							</tbody>
						</table>
					</div>
				</div>
				<!-- 자기소개서 -->
				<div class="container" :class="{save:saveMode}">
					<h1>자기소개서</h1>

					<div 
						class="cover-letter-container table-container" 
						v-for="(cl, index) in coverLetterList" 
						:key="index"
					>
						<table>
							<thead>
								<tr>
									<th>
										<input type="text" v-model="cl.title" placeholder="타이틀을 입력해주세요.">
										<div class="btn-group" v-if="editMode">
											<button 
												class="btn delete" 
												:disabled="index==0" 
												:class="{disabled:index==0}" 
												@click="openConfirm(cl,index)"
											>
												삭제
											</button>
										</div>
									</th>
								</tr>
							</thead>
							<tbody>
								<tr>
									<td class="cover-letter">
										<div 
											class="textarea-div ta-l" 
											:contenteditable="editMode" 
											@input="inputText(cl, $event, 'content')"
										> 
											{{cl.content}}
										</div>
									</td>
								</tr>
							</tbody>
						</table>
					</div>
					<div class="cover-letter-add">
						<button 
							class="btn" 
							v-if="editMode"
							@click="addItem.coverLetter"
						>
							추가
						</button>
					</div>
				</div>
			</div>
		</template>
	</vue-html2pdf>
	
	<ConfirmView 
		v-if="showConfirm" 
		@close="showConfirm = false" 
		@delete="deleteItem">
	</ConfirmView>
	</div>
	<footer>
        <div class="footer-wrap">
            <p>Copyright © SEUNGMIN HAN 2023. All rights reserved.</p>
            <ul class="social-media">
                <li><a href="https://github.com/seungmin-han" target="_blank">github</a></li>
                <li><a href="https://www.instagram.com/1seungm1n/" target="_blank">instagram</a></li>
            </ul>
        </div>
    </footer>
</template>

<script setup>
import { onMounted, reactive, ref, watch } from "vue";
import ConfirmView from "@/components/ConfirmView";
import Draggable from 'vue3-draggable';
import VueHtml2pdf from 'vue3-html2pdf';

	const OPTION_LIST = [
		{value:'-1',title:'졸업 구분을 선택해주세요'},
		{value:'0',title:'재학'},
		{value:'1',title:'졸업'},
		{value:'2',title:'휴학'},
		{value:'3',title:'중퇴'},
	]

	const saveMode = ref(false);
	const isRow = ref(false);
	const showConfirm = ref(false);
	const selectedIndex = ref(null);

	const h2p = ref(null);
	const fileEl = ref(null);

	const profile = reactive({});
	const careerList = reactive([{}]);
	const educationList = reactive([{graduationCode:-1}]);
	const awardList = reactive([{}]);
	const licenseList = reactive([{}]);
	const portfolioList = reactive([{}]);
	const coverLetterList = reactive([{}]);

	const editMode = ref(false);

	const inputText = (target, event, key) => {
		target[key] = event.target.innerHTML;
	}

	const saveToPDF = async () => {
		const editModeOrigin = JSON.parse(JSON.stringify(editMode.value));
		const isRowOrigin = JSON.parse(JSON.stringify(isRow.value));

		saveMode.value = true;
		editMode.value = false;
		isRow.value = false;
		await h2p.value.generatePdf();
		saveMode.value = false;
		editMode.value = editModeOrigin;
		isRow.value = isRowOrigin;
	}

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
		coverLetter: () => {
			coverLetterList.push({});
		}
	}

	const openConfirm = (cl,index) => {
		console.log(cl.title, cl.content);
		if(!cl.title?.length && !cl.content?.length) {
			coverLetterList.splice(index, 1);
		} else {
			selectedIndex.value = index;
			showConfirm.value = true;
		}	
	}

	const deleteItem = () => {
		coverLetterList.splice(selectedIndex.value, 1);
		selectedIndex.value = null;
		showConfirm.value = false;
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
</style>
<style lang="scss" scoped>
	.draggable-comp::v-deep {
		display: table-row;
	}
	.h2p::v-deep {
		&.row {
			section {
				width: 100% !important;
			}
		}
		section {
			margin: 0 auto;
		}
		
	}
	.container-wrap {
		display: flex;
		&.direction-col {
			flex-direction: column;
		}
		&.direction-row {
			flex-direction: row;
		}
		.mt-50 {
			margin-top: 50px;
		}
		.w-100 {
			width: 100%;
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

		.ta-l {
			text-align: left;
		}

		td {
			text-align: center;
			&.profile {
				width: 430px;
				word-break: break-word;
			}

			&.award {
				min-width: 150px;
				max-width: 180px;
				word-break: break-word;
			}

			&.cover-letter {
				width: 730px;
				word-break: break-word;
				> .textarea-div {
					min-height: 30px;
				}
			}

			&.portfolio {
				max-width: 300px;
				word-break: break-word;
			}

			input, textarea {
				padding-left: 5px;
			}
			> input {
				width: 100%;
				height: 30px;
				line-height: 30px;
				text-align: center;
				
			}
			> textarea {
				width: 100%;
				height: auto;
				font-size: 15px;
				outline: none;
				border: none;
				resize: none;
				line-height: 15px;
				padding: 2px;
			}
			> .textarea-div {
				width: 100%;
				min-height: 15px;
				line-height: 15px;
				font-size: 15px;
				padding: 2px;
			}
		}

		.container {
			box-shadow: 0px 0px 20px #000;

			width: 21cm;
			min-height: 29.7cm;
			padding: 30px;
			margin: 20px auto;
			display: flex;
			flex-direction: column;
			align-items: center;

			&.save {
				min-height: auto;
			}

			.profile-container {
				width: 100%;
				display: flex;
				align-items: center;
				justify-content: space-between;

				th {
					padding: 5px;
					text-align: left;
				}

				.profile-img {
					cursor: pointer;
					box-sizing: content-box;
					overflow: hidden;
					border: 1px solid #000;
					width: 125px;
					height: 150px;
					font-size: 12px;
					display: flex;
					align-items: center;
					justify-content: center;
					&.view-mode {
						pointer-events: none;
					}
					&.img-selected {
						background-color: #000;
					}
				}
			}

			.table-container {
				width: 100%;
				table {
					margin-top: 20px;
				}
				
				
			}
		}

		.cover-letter-container {
			margin-top: 30px;
			th {
				height: 30px;
				line-height: 30px;
				// padding: 5px;
				width: 100%;
				display: flex;
				border: none;
				justify-content: space-between;
				input {
					width: 50%;
					font-weight: bold;
					background: none;
				}
			}
		}
		.cover-letter-add {
			width: 100%;
			margin-top: 10px;
		}
	}

	footer {
		width: 100%;
	}

	.footer-wrap {
		margin-top: 20px;
		padding: 10px;
		width: 100%;
		background-color: #444;
		
		p {
			color: #fff;
			text-align: center;
			margin: 20px 0;
		}
		
		> ul {
			text-align: center;
			> li {
				color: #fff;
				display: inline-block;
				padding: 5px 10px;
				a {
					color: #fff;
					text-decoration: underline;
				}				
			}
		}	
	}
</style>
