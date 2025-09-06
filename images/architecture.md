# ClipHaus 아키텍처

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Frontend      │    │   API Services  │    │   AWS Services  │
│   (Next.js)     │    │   (Flask)       │    │                 │
├─────────────────┤    ├─────────────────┤    ├─────────────────┤
│ • 비디오 업로드  │◄──►│ • S3 Upload     │◄──►│ • S3 Storage    │
│ • 비디오 목록    │    │ • Bucket List   │    │ • Bedrock AI    │
│ • AI 편집 요청   │    │ • Video AI      │    │ • Step Functions│
│ • 사용자 인증    │    │ • Sign Up/In    │    │ • Transcribe    │
│ • 결과 확인      │    │                 │    │ • MediaConvert  │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
                    ┌─────────────────┐
                    │   Infrastructure│
                    │   (SAM/Docker)  │
                    ├─────────────────┤
                    │ • Lambda 함수    │
                    │ • EventBridge   │
                    │ • CloudFormation│
                    │ • Docker 컨테이너│
                    └─────────────────┘
```
