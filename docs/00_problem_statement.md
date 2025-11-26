# Problem Statement
## 문제정의
- 자율주행에서 물체가 가려지나 악천후의 상황에선 온보드 센서로는 한계 존재
- 현실의 V2X : 지연, 손실, 순서 뒤집힘, 스푸핑 등 노이즈 및 오염 존재
- 단순 노이즈 검출은 많은 알고리즘 존재

## univ2x 정리
1. Sparse (Instance-level) 전송
 - Agent queries (차량/보행자 등 object-level)
 - Lane queries (lane, road boundary 등 map element)
 - > 차/차선 단위의 high-level feature만 전송해서 통신량 절감
2. Dense (Scene-level) 전송
 - Occupancy probability map (grid-based scene representation)
 - > 