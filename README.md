# Unificação de Datasets de Reciclagem para YOLOv10 Small

Esta branch documenta o processo de extração, limpeza, mapeamento e unificação de três datasets distintos de detecção de lixo/recicláveis. O objetivo é criar uma base de dados única e padronizada (`Dataset_Unificado_YOLO`) para treinar um modelo YOLOv10 Small focado na classificação de resíduos.

## Datasets Utilizados

Os dados originais foram obtidos do Roboflow e são compostos pelas seguintes bases:
1. **Recyclable Material Sorting ver2** (Dataset Base - +12k imagens): [Link](https://universe.roboflow.com/yoloissegypt2025/recyclable-material-sorting-ver2/dataset/1)
2. **Waste Detection jlylt** (Complementar): [Link](https://universe.roboflow.com/xavi-ojx7l/waste-detection-jlylt/dataset/2)
3. **Waste Detection nabn2** (Diversificado, sem papelão): [Link](https://universe.roboflow.com/sb-cyjyi/waste-detection-nabn2/dataset/8)

## Padronização de Classes

O objetivo principal foi alinhar as anotações de todos os datasets para um padrão único de **5 classes**, essenciais para a nossa aplicação:

* `0: metal`
* `1: cardboard` (papelão)
* `2: glass` (vidro)
* `3: paper` (papel)
* `4: plastic` (plástico)

### Etapas de Processamento (`agrupamento.ipynb`):

1. **Análise Inicial:** Foi feito o levantamento dos arquivos para entender as divergências de índices e nomenclaturas nos arquivos `data.yaml` de cada dataset.
2. **Mapeamento e Correção de Labels:**
   * No segundo dataset (`waste detection.v2i`), as 5 classes estavam em ordens diferentes (ex: CARDBOARD era 0, GLASS era 1). Um script corrigiu **17.537 objetos** em **5.195 arquivos .txt**.
   * No terceiro dataset (`waste detection.v8i`), existiam apenas 4 classes, que também estavam com índices divergentes. O script realinhou **2.144 objetos** em **2.013 arquivos .txt**.
3. **Limpeza de Dados:** * Identificou-se a presença de resíduos "biodegradáveis" no segundo dataset, uma classe que foge do escopo do projeto. O script realizou uma "faxina", removendo **137 imagens e 137 labels (.txt)** associados ao termo `biodegradable`.
4. **Unificação e Prevenção de Colisões:** * Todos os datasets foram mesclados nas pastas `train`, `valid` e `test`. 
   * Para evitar que imagens com o mesmo nome fossem sobrescritas, os arquivos foram renomeados com um prefixo indicando a pasta de origem (ex: `Recyclable Material Sorting ver2.v1i.yolov8_nome_original.jpg`).
5. **Geração do YAML Final:** Criação de um novo `data.yaml` compatível com a biblioteca Ultralytics/YOLO.

## 📊 Estatísticas do Dataset Unificado

Após o processamento, o `Dataset_Unificado_YOLO` finalizou com **22.369 pares (imagem + anotação)**.

**Distribuição Total de Objetos (48.613 bounding boxes):**
* 🥫 **Metal (0):** 9.499 objetos (19.5%)
* 📦 **Cardboard (1):** 8.648 objetos (17.8%)
* 🍾 **Glass (2):** 11.848 objetos (24.4%)
* 📄 **Paper (3):** 6.117 objetos (12.6%)
* 🧴 **Plastic (4):** 12.501 objetos (25.7%)

Esta distribuição apresenta um bom balanceamento geral, oferecendo uma base sólida de exemplos variados para a etapa de treinamento do YOLOv10 Small.
