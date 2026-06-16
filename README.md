# Adaptação de YOLOv10 Small para Ambiente Real de Reciclagem

Esta branch documenta o processo de validação, adaptação e *fine-tuning* de um modelo YOLOv10 Small, originalmente treinado para detectar resíduos recicláveis em ambientes controlados, para um cenário real de esteira de triagem.

## Contexto e Problema
O modelo base foi treinado utilizando uma união de três datasets do Roboflow (com bounding boxes simples, objetos individuais e frequentemente em fundo branco), contendo **5 classes**: `['metal', 'cardboard', 'glass', 'paper', 'plastic']`. 

Surgiu a dúvida sobre a eficácia desse modelo em um ambiente real. Para validar essa hipótese, utilizamos o dataset **zerowaste-f-final** (baseado no dataset *ZeroWaste* da Boston University e referenciado em um estudo da USP), que contém imagens catalogadas de uma esteira de reciclagem.

## Pré-processamento e Preparação de Dados

1. **Conversão e Formatação:** Os dados foram convertidos para o formato Roboflow/Ultralytics e todas as imagens padronizadas para `.jpg`.
2. **Mapeamento de Classes:** O dataset da esteira possuía 4 classes, que foram mapeadas para se adequarem às nossas classes alvo. Plásticos rígidos e maleáveis foram unificados.
```python
   # Original: ['rigid_plastic', 'cardboard', 'metal', 'soft_plastic']
   # Alvo:     ['metal', 'cardboard', 'glass', 'paper', 'plastic']
   MAPEAMENTO = { 
       0: 4, # rigid_plastic -> plastic
       1: 1, # cardboard -> cardboard
       2: 0, # metal -> metal
       3: 4  # soft_plastic -> plastic 
   }
   ```
3. **Geração do Vídeo de Simulação:** O dataset original da esteira consistia em frames de um vídeo a 120 FPS. Para a simulação, como no dataset original ocorre um salto de 10 frames (1, 11, 21...) decidimos gerar um vídeo de teste a **12 FPS**. O vídeo final possui um mostrador de FPS para simular a velocidade de inferência do modelo e contém amostras de metal, plástico e papelão as 3 classes catalogadas no dataset.

---

## Experimentos e Resultados

### 1. Teste com o Modelo Original (Zero-shot na esteira)
* **Procedimento:** O vídeo da esteira foi processado pelo modelo base pré-treinado.
* **Resultado:** Desempenho insatisfatório. O modelo detectou apenas alguns papéis, pouquíssimos plásticos e latas, com baixa confiança e muitos erros. Impressionantemente, não conseguiu detectar papelão, que estava presente em abundância.

### 2. Retreinamento 1: Adaptação à Esteira
* **Procedimento:** Treinamento de **50 épocas** (com *Early Stopping* em 10 e taxa de aprendizado reduzida) utilizando exclusivamente o dataset da esteira.
* **Resultado:** O modelo apresentou uma melhoria drástica. Passou a identificar com clareza papelão, plástico e metal. 
* **Problema:** Ocorreu um esquecimento catastrófico da classe **papel** (que não estava anotada neste dataset). A confiança para papel caiu para abaixo do *threshold* de **0.25**.

### 3. Retreinamento 2: Tentativa de Preservação de Classe (Esteira + Papel)
* **Procedimento:** Para tentar fazer o modelo enxergar papel na esteira sem perder o aprendizado anterior, combinamos mais de 3.000 imagens da esteira com cerca de 100 imagens de papel do dataset antigo. Foi realizado um treinamento mais conservador: **30 épocas** (com *Early Stopping* em 8) visando preservar os pesos iniciais e não comprometer a detecção da esteira.
* **Resultado:** O modelo **não** conseguiu generalizar e ainda falhou ao tentar detectar papel no contexto da esteira. Além disso, as métricas gerais foram inferiores às do Retreinamento 1. 

---

## Conclusão

Os experimentos demonstram que, embora o modelo YOLOv10 Small possa ser aplicado no mundo real, **treinamentos com imagens de fundo branco não generalizam bem para ambientes complexos**. 

Para obter um modelo funcional e robusto em uma esteira de reciclagem real, não é estritamente necessário que 100% das imagens da esteira sejam anotadas manualmente. O cenário ideal envolve possuir uma base representativa de dados já catalogados com as **5 classes alvo no ambiente final (na esteira)** e combinar isso com técnicas de **aprendizado semi-supervisionado**. 

Dessa forma, pode-se aproveitar um grande volume de imagens brutas (sem anotação) da própria esteira em uma etapa inicial de treinamento para que o modelo aprenda o contexto visual, seguido de um *fine-tuning* supervisionado focado apenas na parcela de dados corretamente anotados.

*Obs: Os vídeos gerados a partir da inferência de todos os modelos testados durante estes experimentos estão disponíveis no repositório para visualização e comparação.*
