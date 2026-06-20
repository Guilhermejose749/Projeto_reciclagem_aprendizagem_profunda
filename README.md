# Detecção e Classificação de Resíduos Recicláveis com YOLOv10

## Sobre o Projeto

A gestão de resíduos sólidos urbanos é um dos grandes desafios ambientais contemporâneos. Quando não tratados de forma adequada, esses resíduos geram graves impactos ambientais e sociais. 

Neste contexto, este projeto tem como objetivo desenvolver um **modelo de detecção e classificação de resíduos recicláveis** utilizando a arquitetura **YOLOv10** (versão Small). A ideia central é que o modelo atue como uma ferramenta de auxílio visual e automatizado no processo de triagem em esteiras de reciclagem, identificando e separando os resíduos em 5 classes principais:
* `metal` (Metal)
* `cardboard` (Papelão)
* `glass` (Vidro)
* `paper` (Papel)
* `plastic` (Plástico)

## Implementação e Validação Inicial

Aqui encontra-se o **Experimento 1** (`fine-tune-yolov10s.ipynb`), que representa a implementação e o treinamento (*fine-tuning*) do modelo base em um ambiente controlado. 

Para este experimento, utilizamos uma base de dados unificada com cerca de 22.500 imagens. O treinamento foi configurado para 30 épocas, visando a convergência em imagens majoritariamente com fundo limpo e objetos bem delimitados.

### Resultados (Ambiente Controlado)
Os resultados obtidos neste primeiro momento foram bastante promissores para o cenário controlado:
* **mAP50 (média):** ~0.640
* A prova de conceito técnica foi validada com sucesso, mostrando que a arquitetura YOLOv10s consegue extrair os padrões corretos para as 5 categorias de recicláveis propostas.

**Próximos Passos:** Embora o modelo apresente um bom desempenho em condições ideais de laboratório (fundo branco, objetos isolados), a aplicação prática exige validação. É fundamental testar o comportamento deste modelo em um cenário de **esteira real**, onde os lixos sofrem oclusão severa, estão empilhados e o fundo da esteira adiciona ruído visual.

## Estrutura de Branches do Projeto

Para manter o fluxo de desenvolvimento organizado e as validações separadas, o projeto foi estruturado nas seguintes branches de estudo e experimentação:

1. **`estudo-modelos` (Estudo de Arquiteturas):** Branch dedicada à pesquisa teórica e testes preliminares para decidir qual modelo utilizar (como a escolha e justificativa pelo YOLOv10 em relação ao custo computacional e tempo de inferência).
2. **`implementacao-inicial` (Ambiente Controlado):** Contém os primeiros testes e a configuração inicial do modelo rodando apenas sobre dados simples, validando a viabilidade do código.
3. **`unificacao-datasets` (Criação do Dataset Unificado):** Documenta a extração, faxina de dados, mapeamento de classes e fusão de três bases de dados distintas do Roboflow. O objetivo foi criar a base robusta de 22.369 imagens que alimenta este experimento principal.
4. **`teste-esteira` (Adaptação para o Cenário Real):** Branch focada em testar a generalização do modelo treinado contra um dataset real de triagem (`zerowaste-f-final`). Contém os experimentos para entender como contornar as falhas do modelo em ambientes complexos (esteiras de reciclagem com oclusões e sujeira).
