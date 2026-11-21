# -학생 성적 관리 사이트입니다. 학생이름 및 성적을 추가 할 수 있고 삭제버튼을 통하여 없앨 수 있습니다. 또한 오른쪽 ui에 평균등급 및 순위가 나옵니다.

[학생 성적 관리.html](https://github.com/user-attachments/files/35210274/default.html)
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>학생 성적 및 학점 관리 시스템</title>
    <!-- Tailwind CSS 로드 -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- 한국어 웹폰트 (Noto Sans KR & Inter) 로드 -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&family=Noto+Sans+KR:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Noto Sans KR', 'Inter', sans-serif;
        }
        /* 부드러운 프로그레스 바 애니메이션 */
        .bar-transition {
            transition: width 0.5s cubic-bezier(0.4, 0, 0.2, 1);
        }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen flex flex-col">

    <!-- 상단 헤더 영역 -->
    <header class="bg-indigo-700 text-white shadow-md">
        <div class="max-w-6xl mx-auto px-4 sm:px-6 py-5 flex flex-col sm:flex-row sm:items-center sm:justify-between gap-3">
            <div>
                <div class="flex items-center gap-2">
                    <span class="bg-indigo-500 text-xs font-bold px-2.5 py-1 rounded-full tracking-wide">WEB EDITION</span>
                    <h1 class="text-xl sm:text-2xl font-bold tracking-tight">학생 성적 및 학점 관리 시스템</h1>
                </div>
                <p class="text-indigo-200 text-sm mt-1">파이썬 성적 관리 로직 기반 인터랙티브 웹 대시보드</p>
            </div>
            <!-- 테스트 편의성을 위한 빠른 샘플 데이터 및 초기화 버튼 -->
            <div class="flex items-center gap-2 self-start sm:self-auto">
                <button type="button" onclick="loadSampleData()" class="bg-indigo-600 hover:bg-indigo-500 text-white text-xs sm:text-sm font-medium px-3.5 py-2 rounded-lg border border-indigo-400/40 transition shadow-sm cursor-pointer">
                    샘플 데이터 채우기
                </button>
                <button type="button" onclick="requestClearAll()" class="bg-white/10 hover:bg-rose-600 text-white text-xs sm:text-sm font-medium px-3.5 py-2 rounded-lg transition cursor-pointer">
                    전체 초기화
                </button>
            </div>
        </div>
    </header>

    <!-- 메인 컨텐츠 영역 -->
    <main class="flex-1 max-w-6xl w-full mx-auto px-4 sm:px-6 py-6 sm:py-8 space-y-6">

        <!-- 인라인 알림 / 메시지 배너 (alert() 대체용) -->
        <div id="feedbackBanner" class="hidden rounded-xl p-4 text-sm font-medium flex items-center justify-between shadow-sm transition-all">
            <div class="flex items-center gap-2.5">
                <span id="feedbackIcon" class="text-lg"></span>
                <span id="feedbackText"></span>
            </div>
            <button type="button" onclick="hideBanner()" class="text-current opacity-70 hover:opacity-100 px-2 py-1 rounded-lg">✕</button>
        </div>

        <!-- 전체 초기화 확인 배너 (confirm() 대체용) -->
        <div id="confirmClearBanner" class="hidden bg-rose-50 border border-rose-200 text-rose-800 rounded-xl p-4 flex flex-col sm:flex-row sm:items-center justify-between gap-3 shadow-sm">
            <div class="text-sm font-medium">
                ⚠️ 등록된 모든 학생 성적 데이터를 삭제하시겠습니까? 이 작업은 되돌릴 수 없습니다.
            </div>
            <div class="flex items-center gap-2 self-end sm:self-auto">
                <button type="button" onclick="cancelClearAll()" class="px-3 py-1.5 text-xs font-semibold bg-white text-slate-700 border border-slate-300 rounded-lg hover:bg-slate-100 transition">
                    취소
                </button>
                <button type="button" onclick="confirmClearAll()" class="px-3 py-1.5 text-xs font-semibold bg-rose-600 text-white rounded-lg hover:bg-rose-700 transition">
                    예, 모두 삭제합니다
                </button>
            </div>
        </div>

        <!-- 1. 성적 입력 카드 섹션 -->
        <section class="bg-white rounded-2xl shadow-sm border border-slate-200/80 p-5 sm:p-6">
            <h2 class="text-base sm:text-lg font-bold text-slate-800 mb-4 flex items-center gap-2">
                <span class="w-2 h-5 bg-indigo-600 rounded-full inline-block"></span>
                새로운 학생 성적 등록
            </h2>
            <form id="gradeForm" onsubmit="handleAddStudent(event)" class="grid grid-cols-1 sm:grid-cols-12 gap-4 items-end">
                <div class="sm:col-span-5">
                    <label for="studentName" class="block text-xs font-semibold text-slate-600 mb-1.5">학생 이름</label>
                    <input type="text" id="studentName" placeholder="예: 김철수" autocomplete="off"
                        class="w-full px-4 py-2.5 rounded-xl border border-slate-300 text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition">
                </div>
                <div class="sm:col-span-4">
                    <label for="studentScore" class="block text-xs font-semibold text-slate-600 mb-1.5">점수 (0 ~ 100점)</label>
                    <input type="number" id="studentScore" placeholder="예: 88" min="0" max="100" step="1"
                        class="w-full px-4 py-2.5 rounded-xl border border-slate-300 text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition">
                </div>
                <div class="sm:col-span-3">
                    <button type="submit"
                        class="w-full bg-indigo-600 hover:bg-indigo-700 active:bg-indigo-800 text-white font-semibold text-sm py-2.5 px-5 rounded-xl shadow-sm transition flex items-center justify-center gap-1.5 cursor-pointer">
                        <span>+ 성적 추가하기</span>
                    </button>
                </div>
            </form>
        </section>

        <!-- 2. 실시간 통계 요약 대시보드 (4개 KPI 카드) -->
        <section class="grid grid-cols-2 lg:grid-cols-4 gap-4">
            <div class="bg-white rounded-2xl p-5 border border-slate-200/80 shadow-sm">
                <p class="text-xs font-semibold text-slate-500">총 학생 수</p>
                <div class="mt-2 flex items-baseline gap-1">
                    <span id="statCount" class="text-2xl sm:text-3xl font-bold text-slate-800">0</span>
                    <span class="text-sm text-slate-500">명</span>
                </div>
            </div>
            <div class="bg-white rounded-2xl p-5 border border-slate-200/80 shadow-sm">
                <p class="text-xs font-semibold text-slate-500">전체 평균 점수</p>
                <div class="mt-2 flex items-baseline gap-1">
                    <span id="statAvg" class="text-2xl sm:text-3xl font-bold text-indigo-600">0.00</span>
                    <span class="text-sm text-slate-500">점</span>
                </div>
            </div>
            <div class="bg-white rounded-2xl p-5 border border-slate-200/80 shadow-sm">
                <p class="text-xs font-semibold text-slate-500">최고 점수</p>
                <div class="mt-2 flex items-baseline gap-1">
                    <span id="statMax" class="text-2xl sm:text-3xl font-bold text-emerald-600">-</span>
                    <span id="statMaxUnit" class="text-sm text-slate-500 hidden">점</span>
                </div>
            </div>
            <div class="bg-white rounded-2xl p-5 border border-slate-200/80 shadow-sm">
                <p class="text-xs font-semibold text-slate-500">최저 점수</p>
                <div class="mt-2 flex items-baseline gap-1">
                    <span id="statMin" class="text-2xl sm:text-3xl font-bold text-rose-500">-</span>
                    <span id="statMinUnit" class="text-sm text-slate-500 hidden">점</span>
                </div>
            </div>
        </section>

        <!-- 3. 하단 2단 레이아웃: 좌측 성적 목록 테이블 / 우측 학점별 분포 시각화 차트 -->
        <div class="grid grid-cols-1 lg:grid-cols-12 gap-6 items-start">

            <!-- 좌측: 학생 성적 목록 & 검색/정렬 필터 (8칸) -->
            <section class="lg:col-span-8 bg-white rounded-2xl shadow-sm border border-slate-200/80 overflow-hidden">
                <div class="p-5 sm:p-6 border-b border-slate-100 flex flex-col sm:flex-row sm:items-center justify-between gap-3">
                    <h2 class="text-base sm:text-lg font-bold text-slate-800 flex items-center gap-2">
                        <span class="w-2 h-5 bg-indigo-600 rounded-full inline-block"></span>
                        전체 학생 성적 목록
                    </h2>

                    <!-- 웹 UI 개선 기능: 이름 검색, 학점 필터, 정렬 선택 -->
                    <div class="flex flex-wrap items-center gap-2">
                        <input type="text" id="searchInput" oninput="renderUI()" placeholder="이름 검색..."
                            class="px-3 py-1.5 text-xs rounded-lg border border-slate-300 focus:outline-none focus:ring-2 focus:ring-indigo-500 w-28 sm:w-32">

                        <select id="gradeFilter" onchange="renderUI()"
                            class="px-2.5 py-1.5 text-xs rounded-lg border border-slate-300 bg-white focus:outline-none focus:ring-2 focus:ring-indigo-500">
                            <option value="ALL">전체 학점</option>
                            <option value="A">A 학점</option>
                            <option value="B">B 학점</option>
                            <option value="C">C 학점</option>
                            <option value="D">D 학점</option>
                            <option value="F">F 학점</option>
                        </select>

                        <select id="sortOrder" onchange="renderUI()"
                            class="px-2.5 py-1.5 text-xs rounded-lg border border-slate-300 bg-white focus:outline-none focus:ring-2 focus:ring-indigo-500">
                            <option value="DEFAULT">등록순</option>
                            <option value="DESC">점수 높은순</option>
                            <option value="ASC">점수 낮은순</option>
                        </select>
                    </div>
                </div>

                <!-- 반응형 테이블 래퍼 -->
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="bg-slate-50/80 text-slate-500 text-xs uppercase border-b border-slate-200">
                                <th class="py-3.5 px-4 font-semibold w-16 text-center">번호</th>
                                <th class="py-3.5 px-4 font-semibold">이름</th>
                                <th class="py-3.5 px-4 font-semibold text-right">점수</th>
                                <th class="py-3.5 px-4 font-semibold text-center">학점</th>
                                <th class="py-3.5 px-4 font-semibold text-center w-24">관리</th>
                            </tr>
                        </thead>
                        <tbody id="studentTableBody" class="divide-y divide-slate-100 text-sm">
                            <!-- 자바스크립트로 동적 렌더링됨 -->
                        </tbody>
                    </table>
                </div>

                <!-- 데이터가 없을 때 표시되는 안내 화면 -->
                <div id="emptyState" class="py-14 px-4 text-center">
                    <div class="inline-flex items-center justify-center w-12 h-12 rounded-full bg-slate-100 text-slate-400 mb-3 text-xl">
                        📋
                    </div>
                    <p class="text-slate-600 font-medium text-sm">등록된 학생 데이터가 없습니다.</p>
                    <p class="text-slate-400 text-xs mt-1">위 입력창에서 성적을 추가하거나 우측 상단의 '샘플 데이터 채우기'를 눌러보세요.</p>
                </div>
            </section>

            <!-- 우측: AI 개선 시각화 기능 - 학점별 분포 현황 차트 (4칸) -->
            <section class="lg:col-span-4 bg-white rounded-2xl shadow-sm border border-slate-200/80 p-5 sm:p-6">
                <div class="mb-4">
                    <h2 class="text-base sm:text-lg font-bold text-slate-800 flex items-center gap-2">
                        <span class="w-2 h-5 bg-emerald-500 rounded-full inline-block"></span>
                        학점별 분포 현황
                    </h2>
                    <p class="text-xs text-slate-400 mt-1">A~F 등급별 인원 및 비율 시각화</p>
                </div>

                <div id="distributionContainer" class="space-y-4">
                    <!-- A 학점 -->
                    <div>
                        <div class="flex justify-between text-xs font-semibold mb-1">
                            <span class="text-emerald-700">A 학점 (90점 이상)</span>
                            <span id="distTextA" class="text-slate-600">0명 (0%)</span>
                        </div>
                        <div class="w-full bg-slate-100 rounded-full h-2.5 overflow-hidden">
                            <div id="distBarA" class="bg-emerald-500 h-2.5 rounded-full bar-transition" style="width: 0%"></div>
                        </div>
                    </div>

                    <!-- B 학점 -->
                    <div>
                        <div class="flex justify-between text-xs font-semibold mb-1">
                            <span class="text-blue-700">B 학점 (80~89점)</span>
                            <span id="distTextB" class="text-slate-600">0명 (0%)</span>
                        </div>
                        <div class="w-full bg-slate-100 rounded-full h-2.5 overflow-hidden">
                            <div id="distBarB" class="bg-blue-500 h-2.5 rounded-full bar-transition" style="width: 0%"></div>
                        </div>
                    </div>

                    <!-- C 학점 -->
                    <div>
                        <div class="flex justify-between text-xs font-semibold mb-1">
                            <span class="text-amber-700">C 학점 (70~79점)</span>
                            <span id="distTextC" class="text-slate-600">0명 (0%)</span>
                        </div>
                        <div class="w-full bg-slate-100 rounded-full h-2.5 overflow-hidden">
                            <div id="distBarC" class="bg-amber-400 h-2.5 rounded-full bar-transition" style="width: 0%"></div>
                        </div>
                    </div>

                    <!-- D 학점 -->
                    <div>
                        <div class="flex justify-between text-xs font-semibold mb-1">
                            <span class="text-orange-700">D 학점 (60~69점)</span>
                            <span id="distTextD" class="text-slate-600">0명 (0%)</span>
                        </div>
                        <div class="w-full bg-slate-100 rounded-full h-2.5 overflow-hidden">
                            <div id="distBarD" class="bg-orange-500 h-2.5 rounded-full bar-transition" style="width: 0%"></div>
                        </div>
                    </div>

                    <!-- F 학점 -->
                    <div>
                        <div class="flex justify-between text-xs font-semibold mb-1">
                            <span class="text-rose-700">F 학점 (60점 미만)</span>
                            <span id="distTextF" class="text-slate-600">0명 (0%)</span>
                        </div>
                        <div class="w-full bg-slate-100 rounded-full h-2.5 overflow-hidden">
                            <div id="distBarF" class="bg-rose-500 h-2.5 rounded-full bar-transition" style="width: 0%"></div>
                        </div>
                    </div>
                </div>

                <!-- 학점 기준 안내 박스 -->
                <div class="mt-6 pt-4 border-t border-slate-100 text-xs text-slate-500 space-y-1">
                    <p class="font-semibold text-slate-600">📌 자동 학점 산출 기준</p>
                    <p>• A: 90점~100점 / B: 80점~89점</p>
                    <p>• C: 70점~79점 / D: 60점~69점 / F: 60점 미만</p>
                </div>
            </section>
        </div>
    </main>

    <!-- 하단 푸터 -->
    <footer class="bg-white border-t border-slate-200 py-4 mt-8">
        <div class="max-w-6xl mx-auto px-4 text-center text-xs text-slate-400">
            학생 성적 및 학점 관리 웹 시스템 • Python CLI 프로그램의 웹 인터페이스 구현 버전
        </div>
    </footer>

    <!-- 자바스크립트 로직 (파이썬 1주차 핵심 로직 100% 반영 + UI 상호작용) -->
    <script>
        // 전역 학생 데이터 리스트 (파이썬의 student_list 역할)
        let studentList = [];
        let nextId = 1; // 고유 식별자 부여용

        /**
         * [핵심 함수 1] 점수에 따른 학점 판정 함수
         * 1주차 파이썬 코드의 calculate_grade(score)와 동일한 조건문 구조
         */
        function calculateGrade(score) {
            if (score >= 90) {
                return 'A';
            } else if (score >= 80) {
                return 'B';
            } else if (score >= 70) {
                return 'C';
            } else if (score >= 60) {
                return 'D';
            } else {
                return 'F';
            }
        }

        /**
         * 학점별 배지 스타일 반환 헬퍼 함수
         */
        function getGradeBadgeHTML(grade) {
            const styles = {
                'A': 'bg-emerald-100 text-emerald-700 border-emerald-200',
                'B': 'bg-blue-100 text-blue-700 border-blue-200',
                'C': 'bg-amber-100 text-amber-800 border-amber-200',
                'D': 'bg-orange-100 text-orange-700 border-orange-200',
                'F': 'bg-rose-100 text-rose-700 border-rose-200'
            };
            const badgeClass = styles[grade] || 'bg-slate-100 text-slate-700';
            return `<span class="inline-flex items-center justify-center w-7 h-7 rounded-lg text-xs font-bold border ${badgeClass}">${grade}</span>`;
        }

        /**
         * 상단 인라인 피드백 메시지 배너 표시 (alert() 대체)
         */
        let bannerTimeout = null;
        function showBanner(message, type = 'success') {
            const banner = document.getElementById('feedbackBanner');
            const icon = document.getElementById('feedbackIcon');
            const text = document.getElementById('feedbackText');

            banner.classList.remove('hidden', 'bg-emerald-50', 'text-emerald-800', 'border-emerald-200', 'bg-rose-50', 'text-rose-800', 'border-rose-200', 'border');

            if (type === 'error') {
                banner.classList.add('bg-rose-50', 'text-rose-800', 'border', 'border-rose-200');
                icon.textContent = '⚠️';
            } else {
                banner.classList.add('bg-emerald-50', 'text-emerald-800', 'border', 'border-emerald-200');
                icon.textContent = '✅';
            }

            text.textContent = message;

            if (bannerTimeout) clearTimeout(bannerTimeout);
            bannerTimeout = setTimeout(() => {
                hideBanner();
            }, 3500);
        }

        function hideBanner() {
            document.getElementById('feedbackBanner').classList.add('hidden');
        }

        /**
         * [핵심 함수 2] 폼 제출 시 학생 성적 검증 및 추가 (파이썬의 add_student 역할)
         */
        function handleAddStudent(event) {
            event.preventDefault(); // 페이지 새로고침 방지

            const nameInput = document.getElementById('studentName');
            const scoreInput = document.getElementById('studentScore');

            const name = nameInput.value.trim();
            const rawScore = scoreInput.value.trim();

            // 1. 이름 공백 예외 처리
            if (!name) {
                showBanner('학생 이름을 입력해 주세요.', 'error');
                nameInput.focus();
                return;
            }

            // 2. 점수 숫자 및 범위(0~100) 예외 처리
            if (rawScore === '' || isNaN(rawScore)) {
                showBanner('숫자로 된 점수를 입력해 주세요.', 'error');
                scoreInput.focus();
                return;
            }

            const score = Number(rawScore);
            if (!Number.isInteger(score) || score < 0 || score > 100) {
                showBanner('점수는 0부터 100 사이의 정수여야 합니다.', 'error');
                scoreInput.focus();
                return;
            }

            // 3. 학점 계산 후 리스트에 객체 저장
            const grade = calculateGrade(score);
            studentList.push({
                id: nextId++,
                name: name,
                score: score,
                grade: grade
            });

            // 입력창 초기화 및 포커스 복귀
            nameInput.value = '';
            scoreInput.value = '';
            nameInput.focus();

            showBanner(`${name} 학생의 성적이 등록되었습니다. (점수: ${score}점, 학점: ${grade})`);
            renderUI();
        }

        /**
         * 개별 학생 데이터 삭제 함수
         */
        function deleteStudent(id) {
            const target = studentList.find(s => s.id === id);
            studentList = studentList.filter(s => s.id !== id);
            if (target) {
                showBanner(`${target.name} 학생의 성적 데이터가 삭제되었습니다.`);
            }
            renderUI();
        }

        /**
         * 전체 초기화 확인 UI 제어 (confirm() 대체)
         */
        function requestClearAll() {
            if (studentList.length === 0) {
                showBanner('초기화할 학생 데이터가 없습니다.', 'error');
                return;
            }
            document.getElementById('confirmClearBanner').classList.remove('hidden');
        }

        function cancelClearAll() {
            document.getElementById('confirmClearBanner').classList.add('hidden');
        }

        function confirmClearAll() {
            studentList = [];
            nextId = 1;
            cancelClearAll();
            showBanner('모든 성적 데이터가 초기화되었습니다.');
            renderUI();
        }

        /**
         * 테스트용 샘플 데이터 채우기 기능
         */
        function loadSampleData() {
            const samples = [
                { name: '김철수', score: 94 },
                { name: '이영희', score: 86 },
                { name: '박민수', score: 75 },
                { name: '정지은', score: 63 },
                { name: '최현우', score: 52 }
            ];

            samples.forEach(item => {
                studentList.push({
                    id: nextId++,
                    name: item.name,
                    score: item.score,
                    grade: calculateGrade(item.score)
                });
            });

            showBanner('샘플 학생 데이터 5명이 추가되었습니다.');
            renderUI();
        }

        /**
         * [핵심 함수 3] 전체 성적 목록, 통계, 분포 차트를 화면에 갱신 (파이썬의 show_all_students 확장)
         */
        function renderUI() {
            // 1. 통계 요약 업데이트 (전체 studentList 기준)
            const totalCount = studentList.length;
            document.getElementById('statCount').textContent = totalCount;

            if (totalCount > 0) {
                const scores = studentList.map(s => s.score);
                const totalScore = scores.reduce((acc, cur) => acc + cur, 0);
                const avgScore = (totalScore / totalCount).toFixed(2);
                const maxScore = Math.max(...scores);
                const minScore = Math.min(...scores);

                document.getElementById('statAvg').textContent = avgScore;
                document.getElementById('statMax').textContent = maxScore;
                document.getElementById('statMin').textContent = minScore;
                document.getElementById('statMaxUnit').classList.remove('hidden');
                document.getElementById('statMinUnit').classList.remove('hidden');
            } else {
                document.getElementById('statAvg').textContent = '0.00';
                document.getElementById('statMax').textContent = '-';
                document.getElementById('statMin').textContent = '-';
                document.getElementById('statMaxUnit').classList.add('hidden');
                document.getElementById('statMinUnit').classList.add('hidden');
            }

            // 2. 학점별 분포 시각화 차트 업데이트
            const gradeCounts = { 'A': 0, 'B': 0, 'C': 0, 'D': 0, 'F': 0 };
            studentList.forEach(s => {
                if (gradeCounts[s.grade] !== undefined) {
                    gradeCounts[s.grade]++;
                }
            });

            ['A', 'B', 'C', 'D', 'F'].forEach(grade => {
                const count = gradeCounts[grade];
                const percent = totalCount > 0 ? Math.round((count / totalCount) * 100) : 0;
                document.getElementById(`distText${grade}`).textContent = `${count}명 (${percent}%)`;
                document.getElementById(`distBar${grade}`).style.width = `${percent}%`;
            });

            // 3. 검색 / 필터링 / 정렬 적용 후 테이블 렌더링
            const searchQuery = document.getElementById('searchInput').value.trim().toLowerCase();
            const gradeFilter = document.getElementById('gradeFilter').value;
            const sortOrder = document.getElementById('sortOrder').value;

            let filteredList = studentList.filter(s => {
                const matchesName = s.name.toLowerCase().includes(searchQuery);
                const matchesGrade = (gradeFilter === 'ALL') || (s.grade === gradeFilter);
                return matchesName && matchesGrade;
            });

            if (sortOrder === 'DESC') {
                filteredList.sort((a, b) => b.score - a.score);
            } else if (sortOrder === 'ASC') {
                filteredList.sort((a, b) => a.score - b.score);
            }

            const tbody = document.getElementById('studentTableBody');
            const emptyState = document.getElementById('emptyState');

            if (filteredList.length === 0) {
                tbody.innerHTML = '';
                emptyState.classList.remove('hidden');
                if (totalCount > 0) {
                    emptyState.querySelector('p.font-medium').textContent = '검색 또는 필터 조건에 일치하는 학생이 없습니다.';
                } else {
                    emptyState.querySelector('p.font-medium').textContent = '등록된 학생 데이터가 없습니다.';
                }
            } else {
                emptyState.classList.add('hidden');
                tbody.innerHTML = filteredList.map((student, index) => `
                    <tr class="hover:bg-slate-50/90 transition">
                        <td class="py-3.5 px-4 text-center text-slate-400 font-medium">${index + 1}</td>
                        <td class="py-3.5 px-4 font-semibold text-slate-800">${escapeHTML(student.name)}</td>
                        <td class="py-3.5 px-4 text-right font-semibold text-slate-700">${student.score}점</td>
                        <td class="py-3.5 px-4 text-center">${getGradeBadgeHTML(student.grade)}</td>
                        <td class="py-3.5 px-4 text-center">
                            <button type="button" onclick="deleteStudent(${student.id})"
                                class="text-xs font-medium text-rose-600 hover:text-rose-800 hover:bg-rose-50 px-2.5 py-1.5 rounded-lg transition cursor-pointer">
                                삭제
                            </button>
                        </td>
                    </tr>
                `).join('');
            }
        }

        /**
         * XSS 방지를 위한 HTML 이스케이프 처리
         */
        function escapeHTML(str) {
            return str.replace(/[&<>"']/g, function(m) {
                return ({
                    '&': '&amp;',
                    '<': '&lt;',
                    '>': '&gt;',
                    '"': '&quot;',
                    "'": '&#39;'
                })[m];
            });
        }

        // 초기 화면 렌더링
        window.onload = function() {
            renderUI();
        };
    </script>
</body>
</html>
