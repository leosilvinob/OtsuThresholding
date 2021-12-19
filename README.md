# Limiarização de imagens - método de Otsu
### Autor: [Leonardo Silvino Brito](https://github.com/leosilvinob)
#### Trainee da equipe de Inteligência Artifical @ Asa Branca Aerospace

## Introdução

 Limiarização é um processo de segmentação de imagens que se baseia na diferença dos níveis de cinza que compõe diferentes objetos de uma imagem. 
  A partir de um limiar estabelecido de acordo com as características dos objetos que se quer isolar, a imagem pode ser segmentada em dois grupos: o grupo de pixels com níveis de cinza abaixo do limiar e o grupo de pixels com níveis de cinza acima do limiar. Se a intensidade do pixel da imagem de entrada for superior ao do limiar, o pixel é marcado como branco e se o pixel de entrada for menor, ele é marcado como preto.
  
  ## Método de Otsu
O método de Otsu é uma técnica que determina um limiar ótimo considerando uma imagem I, que apresenta melhor funcionamento em imagens cujos histogramas são bimodais. O histograma de tal imagem contém picos, que representam diferentes faixas de valores de intensidade.

A ideia central é separar o histograma da imagem em dois clusters com um limite definido como resultado da minimização da variância ponderada dessas classes denotada por 
	 <img src="https://latex.codecogs.com/svg.image?\sigma^{2}_w(t)." title="\sigma^{2}_w(t)." style="display: inline-block;">
<div align="center">
 	<img src="https://github.com/leosilvinob/Asa-mini/blob/85fdcf3b9a2b869fc5cd562ea44136690145a9bb/Otsus-thresholding-by-Histogram-analysis.png?raw=true" width="40%" style="display: inline-block;">
</div>
A equação completa pode ser determinada por:
<div align="center">
	<img src="https://latex.codecogs.com/svg.image?\sigma^{2}_w(t)&space;=&space;w_1(t)\sigma^{2}_1(t)&space;&plus;&space;w_2(t)\sigma^{2}_2(t)" title="\sigma^{2}_w(t) = w_1(t)\sigma^{2}_1(t) + w_2(t)\sigma^{2}_2(t)" style="display: inline-block;">
</div>
Onde <img src="https://latex.codecogs.com/svg.image?w_1(t),&space;w_2(t)" title="w_1(t), w_2(t)"> são as probabilidades de duas classes dividias por um limiar *t*, que está no intervalo entre 0 e 255.

Sendo *t* o nível de cinza que particiona o histograma da imagem em duas clases(*clusters*) C1 e C2, em que a primeira e a segunda classes compreendem os pixels cujos níveis de cinza pertencem ao  intervalo [1, t] e [t + 1, I], onde I é valor de pixel máximo(255). Assim, pode-se definir as probabilidades;
<div align="center">
	<img src="https://latex.codecogs.com/svg.image?\mu_1(t)&space;=&space;\sum_{i=1}^{t}\frac{iP(i)}{w_1(t)}" title="Probilidade C1}">
</div>

<div align="center">
	<img src="https://latex.codecogs.com/svg.image?\mu_2(t)&space;=&space;\sum_{i=t&plus;1}^{I}\frac{iP(i)}{w_2(t)}" title="Probilidade C2">
</div>
Agora devemos encontrar os dois únicos valores que faltam, <img src="https://latex.codecogs.com/svg.image?\sigma_1^{2}" title="\sigma_1^{2}">  e <img src="https://latex.codecogs.com/svg.image?\sigma_2^{2}" title="\sigma_2^{2}}">, que são as variâncias de C1 e C2 respectivamos.
 <div align="center">
	<img src="https://latex.codecogs.com/svg.image?\sigma_1^{2}(t)&space;=&space;\sum_{i=1}^{t}[i&space;-&space;\mu_1(t)]^2&space;\frac{P(i)}{w_1(t)}," Variância C1">
</div>

 <div align="center">
	<img src="https://latex.codecogs.com/svg.image?\sigma_2^{2}(t)&space;=&space;\sum_{i=t&plus;1}^{I}[i&space;-&space;\mu_2(t)]^2&space;\frac{P(i)}{w_2(t)}." title="Variância C2">
</div>

Para ser obtido o valor do limiar de Otsu, basta calcular o valor de variação entre as classes: <img src="https://latex.codecogs.com/svg.image?\sigma_b^2(t)&space;=&space;w_1(t)w_2(t)[\mu_1(t)&space;-&space;\mu_2(t)]^2" title="Limiar de Otsu"> e encontrar seu valor máximo.

## Implementação
Pseudocódigo:
```
Procedimento: Limiarização pelo método de Otsu
Entrada: image, // Imagem em escala de cinza, his
Saída: R                        // limiar de Otsu

INICIO

VARIAVEIS
histograma = histograma da imagem

bin_edge = conjunto de valores das bordas das celulas do histograma

bin_mids = conjunto de valores nos centros das celulas do histograma

w1 = Somatório de 1 a t do histograma // Probabilidade do Cluster 1
w2 = Somatório de t-1 ao valor do pixel máximo do histograma // Probabilidade do Cluster 2

u1 = (Somatório de 1 a t do histograma*bin_mids)/w1 //Variância do cluster 1
u2= (Somatório de t-1 ao valor do pixel máximo histograma*bin_mids)/w2 //Variância do cluster 2

variância_clusters = w1*w2(u1-u2)²
maxima_variancia = valor_maximo(variância_clusters)
RETORNE bin_mids*maxima_variancia

FIM
```



Python:
```python
import numpy as np
import cv2

one = files.upload()
[i for i in dir(cv2) if i.startswith('IMREAD')]

images = glob.glob('*.jpg') + glob.glob('*.png') + glob.glob('*.jpeg') + glob.glob('*.tiff')
image = cv2.imread(images[0], cv2.IMREAD_GRAYSCALE)


def thresholding(image: np.ndarray) -> np.ndarray:
def thresholding(image: np.ndarray) -> np.ndarray:

  bins_num = 256
  hist, bin_edges = np.histogram(image, bins=bins_num)

  bin_mids = (bin_edges[:-1] + bin_edges[1:]) / 2.
  
  w1 = np.cumsum(hist)
  w2 = np.cumsum(hist[::-1])[::-1]

  u1 = np.cumsum(hist * bin_mids) / weight1
  
  u2 = (np.cumsum((hist * bin_mids)[::-1]) / weight2[::-1])[::-1]

  cluster_variance = weight1[:-1] * weight2[1:] * (mean1[:-1] - mean2[1:]) ** 2

  # Maximize the inter_class_variance function val
  index_of_max_val = np.argmax(inter_class_variance)

  return bin_mids[:-1][index_of_max_val]
 ```
 <div align="center">
 	<img src="https://github.com/leosilvinob/Asa-mini/blob/main/xmascat.jpg?raw=true" width="40%" style="display: inline-block;">
</div>
<div align="center">
 	<img src="https://raw.githubusercontent.com/leosilvinob/Asa-mini/main/otsu.png" width="40%" style="display: inline-block;">
</div>
## Conclusão
Neste projeto foi exposto as caracteristicas da limiarização pelo método de Otsu, assim como seu embasamento matemático, pseudocódigo e seu seu código em *Python* para implementação.

### Referências
Leonardo, Torok. (2015)Método de Otsu. Universidade Federal Fluminense – Instituto de Computação

	
