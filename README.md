# Projeto_reciclagem_aprendizagem_profunda - Experimentos YOLO — COCO8 (Toy Dataset)

Treinamento de 6 modelos YOLO nas variantes **nano** e **small** usando o dataset [COCO8](https://docs.ultralytics.com/datasets/detect/coco8/) como base de validação de implementação. A **YOLOv8** é utilizada como modelo baseline de referência.

> **Configuração:** 50 épocas · `imgsz=640` · dataset COCO8 (8 imagens, 4 classes)

---

## Modelos Avaliados

| Modelo | Variante | Backbone |
|--------|----------|----------|
| YOLOv8n | nano | baseline |
| YOLOv8s | small | baseline |
| YOLOv10n | nano | comparação |
| YOLOv10s | small | comparação |
| YOLO26n | nano | comparação |
| YOLO26s | small | comparação |

---

## Resultados

### Métricas Finais

| Modelo | mAP50 ↑ | mAP50-95 ↑ | Precision | Recall | Val Box Loss ↓ | Tempo (s) |
|--------|---------|------------|-----------|--------|----------------|-----------|
| YOLOv8n | 0.8886 | 0.6402 | 0.9417 | 0.3833 | 1.7680 | 28.3 |
| YOLOv8s | **0.9409** | 0.7076 | **0.9739** | 0.5227 | **1.3800** | 68.0 |
| YOLOv10n | 0.8668 | 0.6404 | **0.9835** | 0.5265 | 1.6027 | 44.9 |
| YOLOv10s | 0.9307 | **0.7609** | 0.8747 | **0.8169** | 1.4356 | 54.2 |
| YOLO26n | 0.9062 | 0.6765 | 0.7005 | 0.6770 | 1.6046 | 49.3 |
| YOLO26s | 0.8864 | 0.6839 | 0.5807 | **0.8808** | 1.4706 | 64.0 |

> ↑ maior é melhor · ↓ menor é melhor

---

## Análise por Modelo

### YOLOv8n — Baseline nano
Melhor tempo de treino (28s), mas com o menor recall (0.38) entre todos os modelos. Precision alta (0.94), o que indica que o modelo é conservador: detecta com confiança, mas erra por omissão. Esperado para um modelo leve sem otimizações arquiteturais posteriores.

### YOLOv8s — Baseline small
Melhor mAP50 geral (0.9409) e menor val box loss (1.38), confirmando a força do baseline em datasets simples. O tempo de treino foi o maior (68s), mas a qualidade das predições compensou. Referência sólida para comparação.

### YOLOv10n
mAP50 ligeiramente abaixo do YOLOv8n (0.8668), mas com recall melhor (0.52) e precision mais alta (0.98). O tempo de treino (45s) foi maior que o baseline nano, o que pode indicar custo adicional da arquitetura NMS-free. Não superou o baseline na métrica principal.

### YOLOv10s
Melhor mAP50-95 (0.7609) e recall (0.82) entre todos os modelos, com tempo razoável (54s). Demonstra boa generalização e capacidade de detectar mais objetos corretos. Competitivo com o YOLOv8s em desempenho geral, com vantagem no recall.

### YOLO26n
mAP50 intermediário (0.9062), acima do YOLOv8n. Precision baixa (0.70) e recall mediano (0.68), sugerindo um equilíbrio menos afinado neste dataset pequeno. O tempo (49s) foi maior que o YOLOv8n sem ganho equivalente em mAP50.

### YOLO26s
mAP50 abaixo do baseline small (0.8864 vs 0.9409), com precision muito baixa (0.58) mas recall alto (0.88). O modelo detecta muito, mas com muitos falsos positivos. Pode se beneficiar de mais épocas ou ajuste de threshold de confiança.

---

## Observações

- COCO8 é um dataset de brinquedo com apenas 8 imagens: os resultados são úteis para validar a implementação, não para conclusões definitivas sobre desempenho real.
- A variante **small** tende a superar a **nano** em mAP, ao custo de mais tempo de treino.
- O **YOLOv10s** se destaca pelo maior recall e mAP50-95, enquanto o **YOLOv8s** lidera em mAP50 e precisão.
- Os modelos **YOLO26** mostram maior desequilíbrio entre precision e recall neste cenário, o que pode mudar com datasets maiores.

---
