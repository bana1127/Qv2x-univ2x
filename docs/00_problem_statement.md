# Problem Statement
## 문제정의
- 자율주행에서 물체가 가려지나 악천후의 상황에선 온보드 센서로는 한계 존재
- 현실의 V2X : 지연, 손실, 순서 뒤집힘, 스푸핑 등 노이즈 및 오염 존재
- 단순 노이즈 검출은 많은 알고리즘 존재
---
## univ2x 정리
1. Sparse (Instance-level) 전송
 - Agent queries (차량/보행자 등 object-level)
 - Lane queries (lane, road boundary 등 map element)
 - > 차/차선 단위의 high-level feature만 전송해서 통신량 절감
2. Dense (Scene-level) 전송
 - Occupancy probability map (grid-based scene representation)
 - > 주변 장애물, static object, free space 같은 거를 grid에서 요약한 맵
3. 시간 동기화 (Temporal sync)
 - infra 쪽 query/occ map을 ego 시점으로 맞추기 위해 **feature flow("query flow")**로 warp
 - 수식상으로 (t_v - t_i) * Q_flow 형태로 보정하는 구조
4. 공간 동기화 (Spatial sync)
 - infra 좌표계 -> ego BEV 좌표계로 transform (pose 사용)
- 그 후 cross-view fusion (AgentFusion, LaneFusion, OccFusion 같은 모듈)으로 통합
5. End-to-End 학습
 - 최종 planning loss + 중간 task loss들 (det, map, occ 등)을 함께 사용해서 joint training
 - 결과적으로 perception/occupancy 성능도 오르고, planning collision rate도 줄어드는 것을 보임
---
## univ2x 한계
1. V2X 링크 품질을 계량화해서 쓰지 않음
 - univ2x는 bandwidth 제한을 가정하고 설계하지만 실제 학습/추론 과정에서 지연, 패킷 손실률, 타임스탬프 mismatch, 채널 혼잡 같은 동적인 통신 품질 지표를 입력으로 사용하지 않음
 - infra 정보에 대한 신뢰평가가 없음
 - 항상 v2x 협력 정보가 잘 도달되었다는 전제하에 fusion을 수행

**링크별/메시지별 신뢰도 벡터 Q(t)를 추정하여 모듈 가중치로 넣거나 ignore**

2. Robusteness to Degraded/Adversarial v2x가 부족
 - UniV2X는 공격/데이터 손상 위험이 있으니 interpretable representation이 좋다고 하지만 실제 공격 시나리오, spoofed infra, 부분 outage를 넣고 학습/평가한 결과가 없음

**통신 계층 + perception 계층에서 uncertainty/inconsistency를 보고 infra occupancy map이 ego 자차 센서와 너무 안맞는다고 판단하면 Q를 낮게, planner에서 v2x 정보를 무시하고 self-perception 기반으로 플래닝**

3. Cross-layer 관점이 아니라 주로 perception + planning 레이어에만 초점
 - application 레벨(perception/planning)에서의 end-to-end 최적화에 집중
 - 통신 스택과의 피드백 루프가 없음

**통신-인지-플래닝을 묶는 cross-layer control signal로 설계 가능**

4. 평가가 대부분 open-loop + planning metric 중심
 - Collision rate, L2 trajectory error, detection/mapping/occupancy mAP 등은 잘 비교하지만, 실시간 네트워크 상황 변화에 따른 안전 여유 지표 부족
 - 관련된 내용이 향후 과제에 나옴.

 **통신 품질 -> Q -> 플래닝 정책 변화로 이어지는 폐루프 프레임워크** 
