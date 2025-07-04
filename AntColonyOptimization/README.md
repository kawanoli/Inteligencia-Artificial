# Trabalho Individual – Aplicação de Metaheurística - Unidade 1
### Discente: Kawan Vinicius Feitosa de Oliveira
### Matricula: 20230022187
---

## Problema de Roteamento de Entregas (Vehicle Routing Problem - VRP)

O Problema de Roteamento de Veículos (Vehicle Routing Problem – VRP) é um desafio clássico da área de logística e otimização, com ampla aplicação em empresas que precisam realizar entregas, como transportadoras, serviços postais e grandes varejistas (ex: Amazon, Correios).

O objetivo principal do VRP é minimizar a distância total percorrida por uma frota de veículos que parte de um ou mais depósitos para atender a um conjunto de clientes com diferentes demandas. Essa otimização impacta diretamente na redução de custos operacionais e no aumento da eficiência das operações logísticas.

Esse problema pode ser visto como uma generalização do Problema do Caixeiro Viajante (Travelling Salesman Problem – TSP). Enquanto o TSP busca o menor percurso para um único agente visitar todas as cidades uma única vez e retornar à origem, o VRP introduz múltiplos veículos, restrições de capacidade, janelas de tempo, entre outras variáveis realistas que aumentam significativamente a complexidade do problema.

Cada rota de um veículo no VRP pode ser tratada como um "mini-TSP", e por isso, algoritmos desenvolvidos para o TSP – como Algoritmos Genéticos, Simulated Annealing, Enxame de Partículas, entre outros – são frequentemente adaptados para resolver o VRP, com as devidas modificações para lidar com as novas restrições.

Colocando ambos frente-a-frente, temos algo como:
- TSP: Um vendedor, todas as cidades, uma rota mínima.
- VRP: Vários veículos, múltiplos clientes, múltiplas rotas otimizadas com restrições práticas.

> Na prática, se você resolver o TSP, você resolve uma versão simples do VRP com um único veículo e sem restrições adicionais.

## Usando Ant Colony Optimization para resolver o problema

Para resolver o Problema de Roteamento de Veículos (VRP) neste projeto, foi utilizada a metaheurística Ant Colony Optimization (ACO) — uma abordagem inspirada no comportamento coletivo de formigas na natureza, especialmente na forma como encontram caminhos eficientes entre o ninho e fontes de alimento.

O ACO é uma escolha natural e eficaz para o VRP por diversos motivos:
- Origem natural em problemas de caminho: O ACO foi originalmente proposto para resolver o Problema do Caixeiro Viajante (TSP), o que o torna altamente compatível com o VRP, que é uma generalização do TSP.
- Alta performance em problemas de roteamento: O algoritmo trabalha com grafos e caminhos — exatamente a estrutura que representa as rotas de entrega no VRP.
- Flexibilidade: O ACO permite a fácil incorporação de restrições típicas do VRP, como capacidade dos veículos, janelas de tempo e múltiplas rotas.
- Capacidade de escapar de ótimos locais: Por ser uma técnica baseada em busca estocástica, o ACO ajuda a explorar melhor o espaço de soluções e evita ficar preso em soluções subótimas.

A implementação segue a lógica clássica do ACO, com adaptações específicas para o contexto do VRP:

Cada "formiga" representa uma solução viável, ou seja, um conjunto de rotas que atendem todos os clientes, respeitando a capacidade dos veículos. A escolha do próximo cliente é baseada em uma combinação de feromônio (trilhas aprendidas) e heurística (como a distância entre os pontos). Quando a capacidade do veículo atinge o limite, a formiga retorna ao depósito e inicia uma nova rota.

A heurística e a probabilidade de transição são dadas por:

$$
\eta_{ij} = \frac{1}{\text{distância}_{ij}}
$$

$$
p_{ij} = \frac{[\tau_{ij}]^\alpha \cdot [\eta_{ij}]^\beta}{\sum\limits_{k \in \text{clientes não visitados}} [\tau_{ik}]^\alpha \cdot [\eta_{ik}]^\beta}
$$

**Onde:**
- $\tau_{ij}$: quantidade de feromônio na aresta $i \rightarrow j$  
- $\eta_{ij}$: valor heurístico (inverso da distância)  
- $\alpha$, $\beta$: parâmetros que controlam a influência do feromônio e da heurística, respectivamente


Após todas as formigas construírem suas soluções, o feromônio é atualizado; a melhor solução (ou um subconjunto das melhores) deposita mais feromônio nas arestas utilizadas.

Também ocorre a evaporação do feromônio para evitar estagnação da busca. O ACO aproveita tanto a exploração (novas rotas) quanto a intensificação (reforço de boas rotas), características centrais dessa metaheurística.

## Bibliotecas Necessárias e Como Executar

Este projeto utiliza algumas bibliotecas Python para realizar as ações necessárias que queremos. Antes de executar o código, certifique-se de ter as seguintes bibliotecas instaladas

```
import numpy as np
import random
import matplotlib.pyplot as plt
```

Caso não tenha, execute a primeira cédula executável do notebook do projeto (.ipynb). O comando  "%" faz com que a linha seja interpretada como comando de terminal, desta forma, não sendo necessário abrir um novo terminal para baixar as bibliotecas.

```python
%pip install numpy matplotlib
```

## Resultados obtidos

A aplicação do algoritmo Ant Colony Optimization (ACO) ao problema de roteamento de veículos (VRP) resultou na construção de rotas que buscam minimizar a distância total percorrida, respeitando as restrições de capacidade de carga dos veículos. No cenário testado, composto por 3 veículos com capacidade de 50 unidades cada e 25 clientes com demanda de 10 unidades, foi possível observar que o algoritmo não conseguiu atender a todos os clientes — o que era esperado, visto que a capacidade total da frota (150 unidades) é inferior à demanda agregada (250 unidades).

As rotas geradas tendem a agrupar clientes próximos uns dos outros e do depósito, resultado da combinação entre o nível de feromônio acumulado nas arestas e a visibilidade heurística (inverso da distância). Isso demonstra que o ACO é eficaz em encontrar boas soluções locais, mesmo em cenários com restrições significativas.

Durante os testes, algumas limitações do ACO tornaram-se evidentes. Por se tratar de uma metaheurística estocástica, o algoritmo não garante a cobertura total da demanda quando a capacidade da frota é insuficiente. Além disso, o ACO não necessariamente escolhe os clientes mais vantajosos para serem atendidos, em termos de custo-benefício ou distância total economizada.

O processo de escolha dos próximos clientes é probabilístico, baseado na influência do feromônio e da heurística local, e não em critérios determinísticos de maximização de cobertura. Isso pode resultar em soluções onde os veículos atendem subconjuntos de clientes que são geograficamente próximos, mas que não maximizam o número de entregas possíveis dentro da capacidade disponível.

A expectativa inicial era que o algoritmo distribuísse as entregas de maneira estratégica, maximizando o número de clientes atendidos dentro dos limites de carga da frota. No entanto, o comportamento observado mostrou que o ACO tende a formar rotas otimizadas localmente, favorecendo grupos de clientes próximos sem uma estratégia global clara.

Como resultado, alguns veículos completaram rotas com combinações de clientes que, embora eficientes do ponto de vista da distância, não representam a melhor alocação possível para maximizar a cobertura. Isso destaca uma limitação importante da abordagem padrão do ACO, especialmente em contextos onde os recursos são restritos e a alocação precisa ser mais seletiva.

## Comentários e conclusões

Os experimentos realizados no notebook demonstraram que o ACO é uma abordagem viável e eficiente para resolver o VRP em cenários com capacidade suficiente, fornecendo soluções que respeitam as restrições operacionais e resultam em rotas curtas e bem estruturadas com um baixo custo computacional.

Contudo, em situações em que a capacidade da frota é insuficiente para atender toda a demanda, o algoritmo pode apresentar comportamentos subótimos, não priorizando necessariamente a maximização da cobertura de clientes. Isso evidencia os problemas das metahuerísticas quando são planejadas para problemas que não estão dentro de seus escopos, e fazendo com que haja necessidade de aprimoramentos, como critérios de seleção mais estratégicos ou mesmo híbridos com outras técnicas de IA para garantir soluções mais robustas em ambientes com restrições de operação.