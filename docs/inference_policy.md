# 최종 정책 행동 선택 의사코드

아래 의사코드는 공개 가능한 범위에서 최종 정책의 행동 선택 흐름을 설명합니다.
프로젝트 원본 구현을 그대로 복사한 소스 코드는 아닙니다.

```python
for step in range(MAX_STEPS):
    current_state = environment.state
    observation = environment.observe()
    valid_actions = observation.action_mask

    probabilities = {
        "lr": lr.predict_proba(state_vector_mask(observation)),
        "et": extra_trees.predict_proba(agent_relative_spatial(observation)),
        "cb": catboost.predict_proba(agent_relative_spatial(observation)),
    }

    blended = (
        0.03000 * probabilities["lr"]
        + 0.32495 * probabilities["et"]
        + 0.64505 * probabilities["cb"]
    )
    blended = mask_and_normalize(blended, valid_actions)

    score = negative_infinity_for_invalid_actions(valid_actions)
    for action in indices_where(valid_actions):
        next_state = environment.preview(action)
        score[action] = (
            log(max(blended[action], 1e-12))
            - 1.0 * next_state_visit_count[next_state]
            - 0.5 * state_action_visit_count[current_state, action]
        )

    selected_action = argmax(score)
    state_action_visit_count[current_state, selected_action] += 1
    environment.step(selected_action)
    next_state_visit_count[environment.state] += 1

    if environment.delivery_completed:
        break
```

## 설계 해석

- 세 모델의 확률을 결합하므로 단일 모델 정책이 아닙니다.
- 유효하지 않은 행동은 Action mask로 선택 대상에서 제외합니다.
- 반복 상태와 같은 상태·행동 쌍에는 누적 패널티를 적용하지만 완전히 금지하지는
  않습니다. 따라서 막다른 길에서 필요한 후퇴는 허용합니다.
- Manhattan progress 보상은 최종 정책에서 `0.0`이므로 행동 점수에 영향을
  주지 않습니다.
- BFS는 Train 레이아웃의 최적 행동 라벨 생성과 평가 후 최단 Step 채점에만
  사용하며, 위 추론 경로에는 BFS 거리·정답 행동·정답 경로가 들어오지 않습니다.
