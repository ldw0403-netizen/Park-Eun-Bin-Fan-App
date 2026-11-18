# Bining v1.7.0 릴리즈 노트 (2026-11-18)

**Park Eun Bin Official Fan App & Digital Frame — BGM & 음악 확장 업데이트**

| 항목 | 값 |
|---|---|
| 버전 | versionName **1.7.0** / versionCode **7** |
| 패키지 | com.parkeunbin.frame · 런처 이름 `💖 Bining 💖` |
| 기반 | **실제 v1.6.0 위그 코드 그대로 승계** (홈 풀스크린·스플래시·노티스 카드형 리스트·날씨 지역 모달 포함) + BGM 시스템 이식 |
| 런타임 | Capacitor 5.7.8 · @capacitor/filesystem · @capacitor/app · targetSdk 34 · minSdk 22 |
| APK | `Bining_v1.7.0_signed.apk` (약 106.6MB — 내장 사진 488장 + 스플래시 11종 + 기본 BGM 12곡) |

> 참고: 이전 세션의 v1.6.0 네이티브 프로젝트는 환경 재활용으로 유실되어, 유일하게 남아 있던
> `Bining_v1.6.0_signed.apk`의 www(58KB index.html)와 리소스(스플래시·아이콘)를 **원본 그대로** 추출해
> 기반으로 삼고, 그 위에 v1.7.0 BGM 기능을 이식했습니다. v1.6.0의 모든 기능은 유지됩니다.

---

## 1. 기본 BGM 소스 연동 (내장 앨범)

- 박은빈 주요 트랙 **12곡**을 앱에 내장했습니다 (`assets/public/music/bgm_01~12.opus`, Ogg Opus).
  1. DREAMS COME TRUE  2. A Melody in Snow  3. Do-re-mi-fa  4. If I Close my eyes and count ten
  5. Into The Light  6. NOW  7. Paper airplane  8. PRISM
  9. Someday (Band Ver.)  10. Sunrise  11. Watercolor  12. it's Okay
- 설정의 **자동 재생**이 ON이면 앱 실행 직후 재생을 시작하고, 마지막 곡까지 재생하면 **처음으로 돌아가 무한 루프**합니다(기본: 순차 반복).
- 네이티브 WebView의 자동재생 제스처 제한을 해제(`setMediaPlaybackRequiresUserGesture(false)`)해 앱 실행 즉시 재생됩니다. 브라우저 미리보기에서는 정책상 첫 터치 후 시작됩니다.
- 마지막 재생 곡 위치를 저장해 다음 실행 때 이어서 시작합니다.

## 2. 커스텀 음악 추가 (확장성)

- **기기 파일 추가**: 설정 > BGM 음악 > 음악 파일 추가 — 여러 곡 동시 선택, 앱 전용 영역에 영구 저장(Filesystem 플러그인 + dataURL 저장, SAF 폴백).
- **URL 직접 추가**: 같은 카드 입력창에 mp3/ogg/opus/m4a 등 오디오 URL을 붙여넣고 [URL] 버튼.
- **Firebase 연동(구조 내장)**: Firestore `bgm_tracks` 컬렉션에 문서 `{ title: "곡명", url: "https://…mp3" }`를 추가하면 **전 기기 플레이리스트에 실시간 자동 반영**됩니다(onSnapshot). 채팅과 동일한 Firebase 프로젝트를 사용합니다.
- 통합 규칙: 기본 12곡은 항상 앞쪽에 유지되고, 커스텀(기기/URL/Firebase) 곡이 뒤에 합쳐집니다. **src 기준 중복 제거**로 어떤 소스를 넣어도 플레이리스트가 충돌/중복 없이 하나의 배열로 관리됩니다.
- v1.5.0/v1.6.0에서 "기기 음악 선택"으로 골둔 곡이 있었다면 첫 실행 때 자동으로 커스텀 트랙으로 승격(마이그레이션)됩니다.

## 3. UI 및 제어 UX

- **좌하단 미니 BGM 도크**(우측 하단 Park EunBin 필기체 로고와 좌우 대칭, 1.6.0의 168px 로고와 무간섭):
  - 원형 버튼 탭 = 재생/일시정지, 옆 트랙명 탭 = 확장 패널(이전/다음 곡, 음량 슬라이더, 총 곡수·재생 방식, 6초 후 자동 접힘).
  - 재생 중에는 핑크 이퀄라이저 바가 움직입니다.
  - 홈의 시계/날씨(좌상단)·로고(우하단)와 간섭 없는 반투명 글래스 스타일, 다른 탭에서는 자동 숨김. 기존 터치 조작 버튼(BGM OFF/ON 필)은 도크로 대체되어 제거했습니다.
- **설정 > 🎵 BGM 음악 카드**: 자동 재생 스위치 / 음량 슬라이더(저장) / 재생 방식(순차 반복·한 곡 반복·셔플) / 파일·URL 추가 / 전체 플레이리스트 목록(출처 배지: 기본·기기·URL·Firebase, 탭하면 해당 곡부터 재생, ✕로 커스텀 곡 삭제).
- 재생 안정성: 재생 불가 파일은 자동으로 다음 곡 건너뛰기(전부 실패 시 정지+안내), 트랙 전환 시 UI 즉시 동기화, 백그라운드 복귀 시 이어 재생 시도, 트랙 교체 중단(AbortError) 무해 처리 — **페이지 오류 0건** 확인.

## 4. v1.6.0 기능 승계 확인 (원본 그대로)

- Immersive Sticky 풀스크린(상태바 완전 숨김)·노치 shortEdges·스플래시(가로/세로 11종)·1.6.0 핑크 아이콘·`💖 Bining 💖` 런처명
- 노티스: GViz(JSONP) 카드형 리스트·📌 중요/NEW 배지·스켈레톤·3회 자동 재시도·오프라인 캐시
- 날씨: 위젯 탭/설정 카드 공용 지역 모달(빠른 선택 10도시 원탭 + 전국·해외 검색)·현재 위치 복원
- 홈 네비 자동 숨김(터치 시 설정 버튼과 함께 등장, 6초 후 소멸)·클램프 시계(최대 58px)·168px 로고

## 5. 설치 안내 (중요)

- v1.6.0 빌드 환경 유실로 서명 키를 **다시 재발급**했습니다(백업 3곳 완료). 기존 설치 앱과 서명이 달라 **덮어쓰기 설치가 불가능**하므로, 기존 Bining 앱을 **삭제한 뒤** 이 APK를 설치해 주세요.
- **이것이 마지막 삭제입니다.** 새 키는 `keys/`(프로젝트 내 2곳) + `download/keystore_backup/`(1곳)에 백업되어 있으며, 다음 업데이트부터는 삭제 없이 업데이트 설치됩니다.

## 6. 개발자 참고

- 웹앱은 단일 파일 `www/index.html`(HTML+CSS+JS 통합, 78KB)입니다. `download/Bining_v1.7.0_www/index.html`에 동일 사본이 있습니다.
- 코드 구조: `MusicStore`(기본·기기·URL·Firebase 트랙 통합) → `BGM`(재생 엔진: load/play/next/prev/mode/volume/오류 스킵) → `paintDock/renderBgmList`(UI 갱신). Firebase `bgm_tracks` 동기화는 `initChat()`의 Firestore 성공 경로에 훅으로 연결.
- 기본 앨범 12곡 외 Inst.(반주) 버전 11곡도 원본 보관(`work/music_src/`) — 향후 연습/반주 모드로 확장 가능.
- 진단 훅: `window.__BGM_DEBUG`(BGM·MusicStore·UI 함수 노출) — 테스트/디버깅용이며 앱 동작에 영향 없음.
- 빌드 환경: Gradle 8.7 · AGP 8.4.2 · Temurin JDK 21 · Android SDK 34 — 스크립트는 `scripts/`에 영구 보관(build_apk.sh, patch_170.py 등).
- 검증: 헤드리스 Chrome에서 재생·순차/셔플·음량·플레이리스트 추가/삭제·영속화·URL/Firebase·노티스·날씨 모달 전 항목 통과, `apksigner verify` 서명 검증 통과.
