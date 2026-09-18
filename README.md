# Preços de imóveis em Belo Horizonte

Análise exploratória e modelagem preditiva de preços de imóveis em Belo Horizonte, a partir de um conjunto de 5.981 anúncios com endereço, área, número de quartos, taxa de condomínio, bairro e coordenadas geográficas.

A pergunta central: **o que realmente determina o preço de um imóvel em BH?**

**Notebook:** `Belo_Horizonte_House_pricing.ipynb` · também pode ser aberto no [Google Colab](https://colab.research.google.com/github/leo-lopes/House-pricing-in-Belo-Horizonte/blob/main/Belo_Horizonte_House_pricing.ipynb)

---

## Os dados

| Coluna | Descrição |
|---|---|
| `address` | Endereço do imóvel |
| `neighborhood` | Bairro |
| `adm-fees` | Taxa de condomínio |
| `price` | Preço de venda |
| `rooms` | Número de quartos |
| `square-foot` | Área |
| `garage-places` | Vagas de garagem |
| `latitude` / `longitude` | Coordenadas |

5.981 registros, com valores ausentes em taxa de condomínio (2.004), preço (30) e bairro (24).

---

## Limpeza dos dados

O conjunto exigiu bastante tratamento antes de qualquer análise:

**Valores ausentes.** Colunas numéricas preenchidas pela média da coluna; bairros ausentes marcados como categoria própria.

**Alta cardinalidade.** A base tem mais de 200 bairros e milhares de endereços distintos, o que inviabiliza o uso direto como variável categórica. Agrupei os bairros com menos ocorrências em uma categoria `Other`, preservando apenas os mais representativos, e removi a coluna de endereço — a informação geográfica fica com latitude e longitude.

**Outliers.** Taxas de condomínio chegavam a R$ 1.500.000 e preços a R$ 130.000.000, claramente erros de cadastro. Apliquei corte em 2× o terceiro quartil para ambas as variáveis, reduzindo a base para 5.452 registros.

**Coluna constante.** 99% dos imóveis são de Belo Horizonte, então a coluna de cidade foi descartada.

---

## O que os dados mostraram

**A taxa de condomínio é o que mais acompanha o preço.** Na matriz de dispersão, a relação entre condomínio e preço é a mais clara de todas.

**A área não se correlaciona com o preço.** Resultado contraintuitivo e o achado mais interessante da análise. A hipótese levantada no notebook é que os imóveis maiores tendem a estar em bairros menos valorizados, o que anularia o efeito da metragem no preço final. Verificar isso exigiria cruzar área com bairro de forma mais controlada.

**Número de quartos e vagas de garagem pouco influenciam.** Os boxplots por faixa mostram distribuições sobrepostas, e ambas as variáveis foram descartadas da modelagem.

**A localização importa, mas de forma concentrada.** O mapa de dispersão colorido por preço (latitude × longitude) mostra os imóveis mais caros na região sul próxima ao centro, com um segundo núcleo a noroeste.

---

## Modelagem

Testei duas abordagens: regressão direta do preço e classificação em faixas de R$ 100 mil.

### Regressão

| Modelo | MAE | MAE / média |
|---|---|---|
| Regressão linear | R$ 367.703 | 48% |
| Lasso (α = 0,0001) | R$ 367.849 | 48% |
| Ridge (α = 0,01) | R$ 367.849 | 48% |
| **Random Forest** | **R$ 239.787** | **31%** |

A regressão linear simples produziu coeficientes da ordem de 10¹⁸ que se anulavam entre si, sinal de multicolinearidade entre as variáveis. Lasso e Ridge corrigiram o problema dos coeficientes sem melhorar o erro, o que indica que a limitação não é de regularização e sim de relação não linear entre variáveis e preço.

O Random Forest reduziu o erro em cerca de um terço, confirmando essa leitura.

### Classificação por faixas

Agrupei os preços em 20 faixas de R$ 100 mil e apliquei Random Forest como classificador: **30% de acerto**. Como o erro do regressor já fica em torno de 31% da média, a classificação não trouxe ganho — a abordagem de regressão é preferível.

---

## Conclusões

1. A taxa de condomínio é o melhor preditor isolado de preço no conjunto analisado.
2. A área não se mostrou correlacionada ao preço, possivelmente por confundimento com localização.
3. Quartos e vagas de garagem têm pouco poder explicativo isolado.
4. Um erro médio de 31% indica que as variáveis disponíveis não capturam boa parte do que forma o preço. Faltam informações sobre estado de conservação, idade do imóvel, andar, infraestrutura do condomínio e proximidade de serviços.

---

## Stack

`Python` · `Pandas` · `NumPy` · `Scikit-learn` · `Matplotlib` · `Seaborn` · `Plotly`

---

## Próximos passos

- [ ] Investigar a relação entre área e bairro para testar a hipótese do confundimento
- [ ] Testar modelos de gradient boosting
- [ ] Usar preço por metro quadrado como alvo, em vez do preço absoluto
- [ ] Incorporar distância a pontos de referência da cidade a partir das coordenadas

---

**Leonardo Lopes** · [GitHub](https://github.com/leo-lopes) · [LinkedIn](https://linkedin.com/in/leo-slopes)
