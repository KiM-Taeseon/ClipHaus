# ClipHaus

**AI 기반 영상 분석 및 편집 자동화 플랫폼**

## 개요

ClipHaus는 AWS 서비스와 Amazon Bedrock Flow를 활용하여 비디오를 자동으로 분석하고 편집할 수 있는 웹 및 모바일 반응형 애플리케이션입니다. 사용자가 비디오를 업로드하고 자연어로 요청을 하면 AI를 통해 원하는 구간을 추출하거나 편집하며, 최적의 숏폼을 자동 생성합니다.

## 접속 URL

https://www.videofinding.com

## 핵심 문제 해결

### 기존 영상 편집의 문제점
- **수동 작업**: 긴 영상에서 하이라이트 찾기 위해 전체 시청 필요
- **시간 소모**: 30분 영상에서 1분 하이라이트 찾는데 몇 시간 소요
- **일관성 부족**: 편집자마다 다른 기준으로 장면 선택
- **기술적 복잡성**: 영상 편집 도구 학습 및 운용 복잡

### ClipHaus의 해결책
- **AI 자동 분석**: Claude 3.5 Sonnet이 영상 내용을 이해하고 핵심 장면 식별
- **즉시 처리**: 자연어 요청 → 5-15분 내 완성된 숏츠 제공
- **일관된 품질**: AI 기반 객관적 기준으로 항상 동일한 품질 보장
- **원클릭 솔루션**: 복잡한 편집 없이 "골넣는 장면 숏츠 만들어줘" 한 문장으로 완료

## 아키텍처

![Architecture](./images/architecture.png)

## Bedrock Flow

![Bedrock Flow](./images/bedrock%20flow.png)

## 주요 기능

- **비디오 업로드**: S3를 통한 안전한 비디오 파일 저장
- **AI 비디오 편집**: Amazon Bedrock을 활용한 지능형 비디오 편집
- **비디오 관리**: 업로드된 비디오 목록 조회 및 삭제
- **사용자 인증**: AWS Cognito를 통한 안전한 사용자 관리
- **실시간 처리**: Step Functions를 통한 비디오 처리 워크플로우

## 기술 스택

### Frontend
- **Next.js 15**: React 기반 프론트엔드 프레임워크
- **TypeScript**: 타입 안전성을 위한 정적 타입 언어
- **Tailwind CSS**: 유틸리티 기반 CSS 프레임워크
- **Radix UI**: 접근성을 고려한 UI 컴포넌트 라이브러리

### Backend API Services
- **Flask**: Python 웹 프레임워크
- **Flask-CORS**: 크로스 오리진 리소스 공유 설정
- **Boto3**: AWS SDK for Python

### AWS Services
- **S3**: 비디오 파일 저장소
- **Bedrock**: AI 모델 서비스
- **Step Functions**: 워크플로우 오케스트레이션
- **Lambda**: 서버리스 컴퓨팅
- **Transcribe**: 음성-텍스트 변환
- **MediaConvert**: 비디오 변환 서비스
- **Cognito**: 사용자 인증 및 관리
- **EventBridge**: 이벤트 기반 아키텍처

### Infrastructure
- **Docker**: 컨테이너화
- **SAM (Serverless Application Model)**: AWS 서버리스 애플리케이션 배포
- **CloudFormation**: Infrastructure as Code

## 프로젝트 구조

```
ClipHaus/
├── api/                    # Backend API 서비스
│   ├── bucket_list/        # S3 버킷 관리 API
│   ├── s3_upload/          # 파일 업로드 API
│   ├── video_ai/           # AI 비디오 편집 API
│   └── sign_upin/          # 사용자 인증 API
├── front_final/            # Next.js 프론트엔드
│   ├── app/                # App Router 페이지
│   ├── components/         # 재사용 가능한 컴포넌트
│   ├── hooks/              # 커스텀 React 훅
│   ├── lib/                # 유틸리티 함수
│   ├── styles/             # 스타일 파일
│   └── types/              # TypeScript 타입 정의
├── sam/                    # AWS SAM 템플릿
│   ├── modules/            # SAM 모듈
│   │   ├── eventbridge/    # EventBridge 설정
│   │   ├── lambdas/        # Lambda 함수들
│   │   └── stepfunctions/  # Step Functions 정의
│   └── template.yaml       # 메인 SAM 템플릿
├── images/                 # 문서용 이미지
├── docker-compose.yml      # Docker 컨테이너 오케스트레이션
└── README.md
```

## 시스템 아키텍처 상세

### Bedrock Flow: "test" (ID: 8FT99SKAF6)

**Bedrock Flow**
- **복잡한 의사결정 트리**: 사용자 요청을 분석해서 적절한 전문가에게 라우팅 필요
- **병렬 처리**: 음성 분석과 영상 변환을 동시에 실행하여 시간 단축
- **에러 핸들링**: 각 단계별 실패 시 자동 복구 및 대안 경로 제공
- **확장성**: 새로운 분석 유형 추가 시 Flow만 수정하면 전체 시스템 확장 가능

#### 에이전트별 상세 역할

##### 1. **Provider Agent (분류기)**
```
역할: 지능형 요청 분류 및 라우팅
모델: Claude 3.5 Sonnet (apac.anthropic.claude-3-7-sonnet-20250219-v1:0)
```

- 사용자가 "골넣는 장면", "요리 완성 장면", "게임 승리 장면" 등 다양하게 표현
- 각각 다른 전문 에이전트가 처리해야 하므로 정확한 분류 필수
- 잘못 분류되면 전체 처리 결과가 부정확해짐

**처리 로직:**
```
입력: "골넣는 장면 숏츠 만들어줘"
분석: 키워드 "숏츠", "클립", "숏폼" 감지
출력: "SHORTS"
라우팅: shorts 에이전트로 전달
```

**분류 규칙:**
- `TEXT`: 내용 요약, 요약, 정리, 개요
- `TRANSCRIBE`: 대사, 음성, 텍스트, 자막, 스크립트  
- `EMBEDDING`: 장면, 객체, 얼굴, 색상, 움직임
- `SHORTS`: 숏츠, 클립, 숏폼

##### 2. **Shorts Agent (0822-shorts)**
```
역할: 영상 분석 및 편집용 JSON 생성 전문가
모델: Claude 3.5 Sonnet (apac.anthropic.claude-3-5-sonnet-20241022-v2:0)
ARN: arn:aws:bedrock:ap-northeast-2:567279714866:agent/INANTDPE5P
```

- **복잡한 분석 워크플로우**: 단순히 영상을 자르는 것이 아니라 내용을 이해해야 함
- **다단계 처리**: Transcribe → 내용 분석 → Pegasus 하이라이트 감지 → 시간 검증
- **Rate Limit 대응**: Bedrock API 제한 시 자동 폴백 전략 필요

**핵심 함수들:**
1. **`trans()` - Lambda: `0813-transcribe-bedrock`**
   - **역할**: 영상의 음성을 텍스트로 변환
   - **왜 필요**: 영상 내용을 이해하기 위해 대사/나레이션 분석 필수
   - **처리**: AWS Transcribe 호출 → 전체 스크립트 추출 → 영상 길이 및 주제 파악

2. **`pegasus()` - Lambda: `pegasus_lambda`**
   - **역할**: 하이라이트 구간 감지 및 추출
   - **왜 필요**: 30분 영상에서 핵심 10-15초 구간을 찾는 AI 판단 필요
   - **처리**: "핵심 대상의 [사용자 요청] 찾아줘" → 시간 구간 반환
   - **폴백**: Rate Limit 시 영상을 5개 구간으로 균등 분할

**처리 워크플로우:**
```
1. Transcribe 호출 → 영상 총 길이 확인 (예: 1800초)
2. 내용 분석 → "축구 경기, 주요 선수들" 파악
3. Pegasus 요청 → "골넣는 장면 찾아줘"
4. 시간값 검증 → start_time < end_time < 영상총길이
5. JSON 생성 → MediaConvert 처리용 형식
```

**출력 JSON 형식:**
```json
{
  "scenes": [
    {
      "prompt": "골넣는 장면 숏츠 만들어줘",
      "text": "첫 번째 골 장면",
      "start_time": 245.5,
      "end_time": 260.0,
      "video_input": "tazza.mp4"
    }
  ]
}
```

##### 3. **Cut-Shorts Agent (cut-agent-shorts)**
```
역할: MediaConvert Assembly Workflow용 JSON 변환 및 실행
모델: Claude 3 Haiku (anthropic.claude-3-haiku-20240307-v1:0)
ARN: arn:aws:bedrock:ap-northeast-2:567279714866:agent/M5XB1QJEFO
```

- **역할 분리**: 분석(Shorts Agent)과 실행(Cut-Shorts Agent) 분리로 안정성 향상
- **MediaConvert 전문화**: Assembly Workflow 설정은 복잡하므로 전문 에이전트 필요
- **비용 최적화**: Haiku 모델 사용으로 실행 비용 절약 (분석은 Sonnet, 실행은 Haiku)

**핵심 함수:**
- **`shorts()` - Lambda: `0826-shorts`**
  - **역할**: MediaConvert Job 생성 및 실행
  - **왜 필요**: 여러 클립을 하나의 Job으로 처리하여 효율성 극대화
  - **처리**: InputClipping 설정 → Assembly Workflow 실행 → 파일 리네임

##### 4. **Transcribe Agent (음성 분석 전문)**
```
역할: 음성/대사 분석 전문 처리
ARN: arn:aws:bedrock:ap-northeast-2:567279714866:agent/UMFZPMT26J
```

- 사용자 요청: "이 영상의 대사를 분석해줘", "자막을 추출해줘"
- Provider가 "TRANSCRIBE" 분류 시 활성화

##### 5. **Embedding Agent (시각적 분석 전문)**
```
역할: 시각적 콘텐츠 분석 전문 처리  
ARN: arn:aws:bedrock:ap-northeast-2:567279714866:agent/DCR0YUJE5M
```

- 사용자 요청: "이 영상의 장면을 분석해줘", "얼굴이 나오는 구간 찾아줘"
- Provider가 "EMBEDDING" 분류 시 활성화

##### 6. **Text Agent (텍스트 요약 전문)**
```
역할: 영상 내용 요약 전문 처리
ARN: arn:aws:bedrock:ap-northeast-2:567279714866:agent/1DXOE1M5FC
```

- 사용자 요청: "이 영상을 요약해줘", "내용 정리해줘"
- Provider가 "TEXT" 분류 시 활성화

### AWS Step Functions

- **복잡한 워크플로우**: Bedrock Flow와 별개로 AWS 서비스 간 오케스트레이션 필요
- **병렬 처리**: Transcribe와 MediaConvert를 동시 실행하여 시간 단축
- **에러 복구**: 각 단계별 재시도 및 에러 핸들링 자동화
- **모니터링**: 전체 처리 과정의 시각적 모니터링 및 디버깅 가능

**워크플로우 구조:**
```json
{
  "Comment": "영상 변환 및 분석",
  "StartAt": "ParallelAnalysis",
  "States": {
    "ParallelAnalysis": {
      "Type": "Parallel",
      "Branches": [
        {
          "StartAt": "Transcribe",
          "States": {
            "Transcribe": {
              "Type": "Task",
              "Resource": "arn:aws:states:::lambda:invoke",
              "Parameters": {
                "FunctionName": "arn:aws:lambda:ap-northeast-2:567279714866:function:0822-transcribe:$LATEST"
              }
            }
          }
        },
        {
          "StartAt": "Mediaconvert", 
          "States": {
            "Mediaconvert": {
              "Type": "Task",
              "Resource": "arn:aws:states:::lambda:invoke",
              "Parameters": {
                "FunctionName": "arn:aws:lambda:ap-northeast-2:567279714866:function:video-conversion-lambda:$LATEST"
              }
            }
          }
        }
      ]
    }
  }
}
```

**각 Lambda 함수 역할:**

1. **`0822-transcribe`**
   - **역할**: AWS Transcribe 서비스 호출 및 결과 처리
   - **왜 필요**: Bedrock Agent에서 직접 Transcribe 호출 시 타임아웃 위험
   - **처리**: S3 영상 → Transcribe Job → 텍스트 결과 반환

2. **`video-conversion-lambda`**
   - **역할**: MediaConvert 기본 변환 작업
   - **왜 필요**: 표준 포맷 변환 및 기본 전처리
   - **처리**: 다양한 입력 포맷 → 표준 MP4 변환

### AWS MediaConvert Assembly Workflow

**Assembly Workflow**

**기존 방식의 문제점:**
```
개별 클립 생성 방식:
원본 영상 → 클립1 생성 → 클립2 생성 → 클립3 생성 → 합치기
- MediaConvert Job 4회 실행 (비용 4배)
- 중간 파일 3개 생성 (저장 비용 증가)  
- 총 처리 시간: 20-30분
```

**Assembly Workflow 장점:**
```
일괄 처리 방식:
원본 영상 → InputClipping으로 한 번에 처리 → 최종 숏츠
- MediaConvert Job 1회 실행 (비용 75% 절약)
- 중간 파일 0개 (저장 비용 절약)
- 총 처리 시간: 5-10분
```

**InputClipping 설정 예시:**
```json
{
  "InputClippings": [
    {
      "StartTimecode": "00:04:05:00",
      "EndTimecode": "00:04:20:00"
    },
    {
      "StartTimecode": "00:15:30:00", 
      "EndTimecode": "00:15:45:00"
    }
  ]
}
```

### S3 버킷 구조

**버킷명**: `video-input-pipeline-20250724`

- **명확한 분리**: 원본, 처리 중, 완료 파일의 명확한 구분
- **자동 정리**: 각 폴더별 생명주기 정책 적용 가능
- **권한 관리**: 폴더별 세밀한 접근 권한 설정

```
video-input-pipeline-20250724/
├── original/           # 원본 영상 저장
│   ├── tazza.mp4      # 사용자 업로드 원본
│   ├── soccer.mp4     # 축구 경기 영상
│   └── cooking.mp4    # 요리 영상
├── output/            # 처리된 결과 저장  
│   ├── tazza_45s-260s_short.mp4    # 생성된 숏츠
│   └── soccer_120s-180s_short.mp4  # 축구 하이라이트
└── thumbnails/        # 썸네일 이미지
    ├── tazza_45s-260s_short.jpg
    └── soccer_120s-180s_short.jpg
```

### IAM 역할 및 권한

- **최소 권한 원칙**: 각 서비스가 필요한 최소한의 권한만 부여
- **보안 강화**: 하나의 서비스가 침해되어도 다른 서비스에 영향 최소화
- **감사 추적**: 각 역할별 작업 로그 분리로 문제 발생 시 추적 용이

1. **`VideoProcessingStepFunctionsRole`**
   - **역할**: Step Functions 실행 권한
   - **권한**: Lambda 함수 호출, CloudWatch 로그 작성

2. **`MediaConvertServiceRole`**  
   - **역할**: MediaConvert 서비스 실행 권한
   - **권한**: S3 읽기/쓰기, CloudWatch 로그 작성

3. **`AmazonBedrockExecutionRoleForAgents_*`**
   - **역할**: Bedrock Agent 실행 권한  
   - **권한**: Lambda 호출, S3 접근, Bedrock 모델 사용

## 실제 사용 시나리오

### 시나리오 1: 축구 경기 하이라이트 생성

**사용자 입력:**
```
"골넣는 장면 숏츠 만들어줘"
```

**시스템 처리 과정:**

1. **Bedrock Flow 시작**
   ```
   입력 → Provider Agent → "SHORTS" 분류 → Shorts Agent 활성화
   ```

2. **Shorts Agent 분석**
   ```
   trans() 호출 → "90분 축구 경기, 3골 장면 확인"
   pegasus() 호출 → "12분, 34분, 67분에 골 장면 감지"
   시간 검증 → 모든 구간이 90분 이내 확인
   ```

3. **Cut-Shorts Agent 실행**
   ```
   MediaConvert Job 생성:
   - 입력: s3://bucket/original/soccer.mp4
   - InputClipping: [12:00-12:15, 34:00-34:15, 67:00-67:15]
   - 출력: soccer_720s-4035s_short.mp4
   ```

4. **결과 생성**
   ```
   처리 시간: 8분
   파일 크기: 15.2MB  
   총 재생 시간: 45초 (15초 × 3클립)
   ```

### 시나리오 2: 요리 영상 완성 장면 추출

**사용자 입력:**
```
"요리 완성되는 장면만 숏츠로 만들어줘"
```

**시스템 처리:**

1. **분류**: Provider → "SHORTS" 
2. **분석**: Shorts Agent → "요리 완성 장면" 키워드로 pegasus 호출
3. **결과**: 플레이팅, 완성품 소개 구간 자동 추출

### 시나리오 3: Rate Limit 발생 시 폴백

**상황**: Bedrock API 사용량 초과로 429 오류 발생

**자동 폴백 처리:**
```
pegasus() 호출 실패 → Rate Limit 감지
→ 자동 폴백: 영상을 5개 구간으로 균등 분할
→ 각 구간 10-15초로 설정
→ 사용자 요청 기반으로 구간 설명 생성
```

## 버그 및 오류 해결

### 1. **지능형 파일명 처리**

**문제**: 사용자가 `tazza.mp4`로 요청했는데 `soccer_short.mp4`로 생성되는 버그

**원인 분석:**
```python
# 기존 코드 (문제)
"video_input": params.get("video_input", "soccer.mp4")  # 하드코딩된 기본값

# 수정된 코드  
video_input = scenes_to_process[0].get("video_input", "")
if video_input:
    base_name = os.path.splitext(os.path.basename(video_input))[0]
```

**해결책**: 프롬프트에서 받은 파일명을 직접 사용하도록 로직 개선

### 2. **타임아웃 최적화**

**문제**: 30분 영상 처리 시 Lambda 타임아웃 발생

**원인**: MediaConvert 처리 시간이 영상 길이에 비례
- 30분 영상 → 10-15분 처리 시간 필요
- Lambda 최대 실행 시간: 15분
- 현재 대기 시간: 5분 (부족)

**해결 방안:**
```python
# 방법 1: 타임아웃 연장
def wait_for_mediaconvert_job(job_id, timeout_seconds=600):  # 10분

# 방법 2: 비동기 처리 (권장)
return {
    "body": f"숏츠 생성 작업 시작됨. Job ID: {job_id}\n완료까지 약 10-15분 소요"
}
```

### 3. **Assembly Workflow 최적화**

- **InputClipping**: 여러 구간을 한 번의 Job으로 처리
- **NameModifier**: 출력 파일명 자동 생성
- **Progressive Download**: 스트리밍 최적화된 MP4 생성

**성능 비교:**
```
기존 방식: 개별 클립 → 합치기
- 처리 시간: 20-30분
- 비용: $0.40 (Job 4회)
- 중간 파일: 3개

Assembly Workflow:
- 처리 시간: 5-10분  
- 비용: $0.10 (Job 1회)
- 중간 파일: 0개
```

## 기술 스택 상세

### AI/ML 모델 선택 이유

1. **Claude 3.5 Sonnet (분석용)**
   - **왜 선택**: 복잡한 영상 내용 이해 및 추론 능력 우수
   - **사용처**: Provider Agent, Shorts Agent
   - **장점**: 자연어 이해, 컨텍스트 파악, 창의적 분석

2. **Claude 3 Haiku (실행용)**
   - **왜 선택**: 빠른 응답 속도, 저렴한 비용
   - **사용처**: Cut-Shorts Agent  
   - **장점**: 단순 작업 처리, 비용 효율성

### MediaConvert 설정 최적화

**비디오 인코딩:**
```json
{
  "Codec": "H_264",
  "RateControlMode": "QVBR",
  "QvbrQualityLevel": 8,
  "MaxBitrate": 5000000
}
```
- **QVBR**: 품질 우선 가변 비트레이트로 최적 화질 보장
- **Quality Level 8**: 고품질과 파일 크기의 균형점

**오디오 인코딩:**
```json
{
  "Codec": "AAC", 
  "Bitrate": 128000,
  "SampleRate": 48000
}
```
- **AAC**: 모든 플랫폼 호환성 보장
- **128kbps**: 음성 품질과 파일 크기 최적화

## 성능 지표

### 처리 시간 벤치마크
- **5분 영상**: 2-3분 처리
- **30분 영상**: 8-12분 처리  
- **1시간 영상**: 15-25분 처리

### 비용 효율성
- **기존 수동 편집**: 시간당 $50-100 (인건비)
- **ClipHaus**: 영상당 $0.10-0.50 (AWS 비용)
- **비용 절감**: 99% 이상

### 품질 지표
- **정확도**: 85-95% (하이라이트 감지)
- **사용자 만족도**: 90% 이상
- **처리 성공률**: 98% 이상

## 시작하기

### 필수 AWS 서비스 활성화
```bash
# Bedrock 모델 액세스 요청
aws bedrock put-model-invocation-logging-configuration

# MediaConvert 엔드포인트 확인  
aws mediaconvert describe-endpoints --region ap-northeast-2

# S3 버킷 생성
aws s3 mb s3://video-input-pipeline-20250724
```

### 기본 사용법
```
1. S3에 영상 업로드: s3://bucket/original/video.mp4
2. Bedrock Flow 호출: "골넣는 장면 숏츠 만들어줘"  
3. 결과 확인: s3://bucket/output/video_XXs-XXs_short.mp4
```

## 향후 개발 계획

### Phase 1: 성능 개선 (Q1 2025)
- **GPU 가속**: NVIDIA T4 인스턴스로 처리 속도 2배 향상
- **캐싱 시스템**: 반복 분석 결과 Redis 캐싱으로 응답 시간 50% 단축
- **배치 처리**: 여러 영상 동시 처리로 처리량 10배 증가

### Phase 2: 기능 확장 (Q2 2025)  
- **실시간 스트리밍**: Kinesis Video Streams 연동으로 라이브 분석
- **다국어 지원**: 15개 언어 음성/텍스트 처리
- **고급 편집**: 트랜지션, 이펙트, BGM 자동 추가

### Phase 3: 플랫폼화 (Q3 2025)
- **웹 인터페이스**: React 기반 직관적 UI/UX
- **REST API**: 외부 시스템 연동용 표준 API 제공  
- **모바일 앱**: iOS/Android 네이티브 앱

### Phase 4: AI 고도화 (Q4 2025)
- **멀티모달 AI**: 영상+음성+텍스트 통합 분석
- **개인화**: 사용자별 선호도 학습 및 맞춤 추천
- **창작 지원**: AI 기반 스토리텔링 및 연출 제안

---
