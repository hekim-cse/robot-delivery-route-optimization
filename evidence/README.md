# 공개 검증 자료

이 디렉터리는 비공개 개발 저장소의 동결 정책과 공식 Final Holdout 결과에서
면접 검토에 필요한 항목만 추려 작성한 공개용 요약본입니다.

## 포함한 자료

- [`final_policy.json`](final_policy.json): 모델 구성, 확률 결합 비중, Decoder 설정
- [`final_holdout_metrics.json`](final_holdout_metrics.json): 사전 등록 기준과 집계 성능
- [`../docs/inference_policy.md`](../docs/inference_policy.md): 행동 선택 의사코드와 BFS 사용 경계

## 제외한 자료

- 대회 원본 데이터와 개별 Episode 경로
- 학습된 모델 내부 파라미터와 직렬화 파일
- 입력·산출물 해시와 내부 파일 경로
- 상세 학습 설정, 실행 로그와 개별 예측값

이 자료는 정책 구조와 보고 수치의 의미를 검토하기 위한 것입니다. 원본
데이터에서 전체 실험을 독립적으로 재실행할 수 있는 재현 패키지는 아닙니다.
