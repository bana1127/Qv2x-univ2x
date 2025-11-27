# Related Work

## Related Work

본 연구는 V2X 기반 협력 인지(Cooperative Perception) 및 엔드투엔드 자율주행 연구를 기반으로 한다. 대표적인 관련 연구로는 V2X-ViT, CoBEVT, UniV2X의 세 가지 계열이 있으며, 아래와 같이 요약된다.

### 1. V2X-ViT: Vision Transformer 기반 Cooperative Perception

V2X-ViT는 차량 및 인프라에서 수집된 멀티뷰 센서 정보를 Transformer 구조로 통합하여 협력적 3D 객체 탐지 성능 향상을 목표로 한다.

핵심 아이디어

에이전트 간 관계를 학습하는 heterogeneous multi-agent self-attention

에이전트 내부 공간 특성을 포착하는 multi-scale window self-attention

두 attention 모듈을 교차 배치하여 이질적인 V2X 환경을 robust하게 처리

기여점

비동기 정보 공유, pose 오차, heterogeneous agent 구조 등을 Transformer 자체가 흡수하도록 설계

CARLA + OpenCDA 기반 대규모 V2X dataset(V2XSet) 제시

기존 V2X detection 대비 SOTA 3D 객체 탐지 성능 달성

한계점(= Q_V2X 진입 지점)

링크 지연, 패킷 손실, timestamp mismatch 등 통신 품질 변화가 모델 입력에 반영되지 않음

특정 에이전트의 노이즈가 attention을 통해 증폭될 위험 존재

Cooperative perception까지만 제공하며, 플래닝·의사결정과는 연결되지 않음

### 2. CoBEVT: Cooperative Bird’s-Eye-View Transformer

CoBEVT는 멀티뷰 카메라 기반 BEV(Bird’s-Eye-View) 표현을 효율적으로 생성하여 고해상도 BEV semantic / occupancy map을 협력적으로 만드는 모델이다.

핵심 아이디어

BEV token을 사용하는 Hierarchical Transformer 아키텍처

에이전트 간 sparse cross-attention 기반 BEV fusion

multi-scale BEV representation으로 고해상도 map을 효율적으로 생성

기여점

V2XSim·OPV2V에서 BEV segmentation/occupancy SOTA 달성

통신량 감소, 계산 효율 향상

Planner-friendly한 BEV 표현 제공

한계점(= Q_V2X 진입 지점)

통신 링크 degradation을 고려하지 않고 모든 에이전트 정보를 동일한 신뢰도로 처리

에이전트 오염(노이즈) 발생 시 고해상도 BEV 전체가 쉽게 오염됨

Planning stage와의 연결 없음 → 안전성 기반 평가 부족

### 3. UniV2X: End-to-End Cooperative Autonomous Driving

UniV2X는 차량·인프라 멀티뷰 센서를 통합하여 인지 → 맵 → occupancy → 플래닝까지 일관된 end-to-end 학습을 수행하는 최신 V2X 자율주행 프레임워크이다.

핵심 아이디어

UniAD 기반 모듈(TrackFormer, MapFormer, OccFormer)을 확장하여 cooperative setting에서 동작

Sparse–Dense Hybrid V2X Fusion:

sparse query(agent/lane) 전송

dense occupancy map 공유

**공간·시간 동기화(temporal sync, spatial transform)**를 통한 멀티에이전트 feature alignment

기여점

perception–prediction–planning을 하나의 구조로 통합한 cooperative end-to-end AD

높은 planning 성공률 및 collision rate 감소 실증

한계점(= Q_V2X 진입 지점)

통신 지연·손실·네트워크 상호 간섭 등 동적 V2X 품질 변동을 반영하지 않는 이상적 가정

모든 V2X 정보가 동일하게 신뢰된다고 가정

통신–인지–플래닝을 연결하는 cross-layer feedback 구조 부재

## Summary: Q_V2X가 필요한 이유

세 연구 모두 V2X 협력 인지 또는 엔드투엔드 자율주행을 성공적으로 구현하였으나, 공통적으로 다음 한계를 지닌다.

(1) 실시간 통신 품질(QoS)에 대한 정량적 추정 모델이 없음

(2) 통신 품질 저하 시 cooperative feature가 오히려 인지/플래닝을 망가뜨릴 위험 존재

(3) V2X 통신–perception–planning을 아우르는 cross-layer optimization 부재

(4) safety margin, risk-aware evaluation 등의 실제 자율주행 요구사항 미반영

따라서 본 연구는 앞선 모델들의 구조적 강점을 유지하되,
V2X 정보의 신뢰도(Q_V2X)를 실시간으로 추정하고 이를 인지·통신·주행 의사결정에 통합적으로 반영하는 교차계층 프레임워크를 제안한다.