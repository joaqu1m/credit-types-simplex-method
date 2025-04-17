# Aplicação do método Simplex na otimização de uma implementação gradual em modalidades de crédito

<!-- Importante antes de começar - Dar uma estudada nos termos técnicos do simplex -->

## Sumário

- [Resumo](#resumo)

<!-- O que é um simplex? Por que escolhi utilizar um modelo simplex? -->
- [Introdução](#1-introdução)

<!-- Qual é o problema? -->
- [Problema](#2-problema)

<!-- Explicando as características técnicas do modelo - Variáveis (e suas motivações), função objetivo, restrições, como isso vai resolver o problema -->
- [Modelo](#3-modelo)

<!-- Mostrando os múltiplos métodos de resolução de problemas LP com pulp, explicando o código, e finalizando com uma análise com sliders -->
- [Aplicação Prática](#4-aplicação-prática)

<!-- Falamos sobre o que esses dados significam, e como vamos nos organizar para a próxima iteração do modelo -->
- [Resultados](#5-resultados)

- [Conclusão](#6-conclusão)

- [Referências](#7-referências)

## Resumo - Retomarei depois

O artigo apresenta um modelo de programação linear (LP) que visa maximizar o lucro total de uma empresa, considerando a implementação gradual de um novo produto. O modelo é baseado em um problema de alocação de recursos, onde o objetivo é determinar a quantidade de cada tipo de cliente a ser atendido a cada 4 meses, levando em conta as restrições de capacidade e demanda.
O modelo é resolvido utilizando o método Simplex, uma técnica amplamente utilizada em programação linear. O artigo também discute a aplicação prática do modelo, incluindo a análise de resultados e a interpretação dos dados obtidos.
O objetivo desse problema é determinar a quantidade de cada tipo de cliente a ser atendido a cada 4 meses, de um modo que tentamos maximizar o lucro total enquanto buscamos descobrir o potencial de todos os recursos disponíveis.

## 1. Introdução

### 1.1. O que é o método Simplex?

No dia a dia de muitas áreas técnicas, como logística, produção, finanças ou engenharia, frequentemente precisamos tomar decisões para usar nossos recursos da melhor forma possível. Pense em definir as melhores rotas de entrega para economizar combustível, organizar a produção em uma fábrica para maximizar a quantidade de produtos, ou escolher investimentos para obter o maior retorno. Basicamente, estamos falando de otimização: encontrar a melhor solução (seja o máximo de algo bom ou o mínimo de algo ruim) dentro de certas regras ou limitações que temos.

Muitos desses problemas se encaixam em uma categoria chamada Programação Linear (PL). A ideia é relativamente simples: são problemas onde tanto o objetivo que você quer alcançar (ex: maximizar lucro) quanto as limitações que você enfrenta (ex: orçamento disponível, horas de trabalho) podem ser descritos usando equações ou inequações lineares – são relações diretas, sem potências ou multiplicações entre as variáveis. Mesmo parecendo simples, a PL é uma ferramenta poderosa para representar e resolver muitas situações práticas.

E como resolvemos esses problemas de Programação Linear? Uma das ferramentas mais conhecidas e fundamentais para isso é o Método Simplex. Criado por George Dantzig nos anos 40, o Simplex é, essencialmente, um algoritmo, uma receita passo a passo. Imagine que todas as soluções possíveis que respeitam suas limitações formam uma espécie de "região" geométrica com cantos bem definidos. O Método Simplex funciona de forma iterativa: ele começa em um desses cantos (uma solução inicial válida) e vai "pulando" para cantos vizinhos, sempre buscando um que melhore o resultado do seu objetivo (mais lucro, menos custo, etc.). Ele continua fazendo isso até encontrar um canto onde não há mais para onde ir para melhorar – esse é o ponto ótimo, a melhor resposta para o seu problema.

Para utilizar o algoritmo Simplex no contexto da programação, você precisa de um software que implemente esse algoritmo. Existem várias opções disponíveis, desde ferramentas comerciais até bibliotecas de código aberto. Uma das mais populares e acessíveis é a PuLP, a biblioteca Python que estaremos utilizando em um ambiente de Jupyter Notebook.

A PuLP é uma biblioteca Python gratuita e de código aberto criada especificamente para modelar problemas de programação linear (e também programação inteira, uma variação). Ela foi desenvolvida inicialmente pela COIN-OR Foundation e outros colaboradores, com o trabalho começando em meados dos anos 2000, e hoje faz parte do ecossistema COIN-OR (Computational Infrastructure for Operations Research), uma iniciativa que reúne diversas ferramentas de pesquisa operacional. A ideia principal por trás da PuLP não é reimplementar o algoritmo Simplex do zero, mas sim fornecer uma interface amigável em Python para que você possa descrever o seu problema de otimização.

A PuLP permite que você traduza os elementos matemáticos da programação linear diretamente para o código Python:
- Definir o Problema: Você cria um objeto que representa seu problema, indicando se quer maximizar ou minimizar algo (por exemplo, LpProblem("NomeDoProblema", LpMaximize)).
- Criar as Variáveis: Você declara as variáveis de decisão (aquelas que o Simplex vai encontrar o valor ótimo), como a quantidade de cada produto a fabricar (usando LpVariable("NomeDaVariavel", lowBound=0) para indicar que não pode ser negativa, por exemplo).
- Adicionar a Função Objetivo: Você escreve a expressão matemática que quer otimizar (como 2\*x + 3\*y) e a adiciona ao seu objeto de problema.
- Adicionar as Restrições: Da mesma forma, você escreve suas limitações (como x + y <= 100) e as adiciona ao problema.

Depois de modelar o problema dessa forma, a PuLP faz a "mágica": ela se comunica com solvers (solucionadores) externos – programas que efetivamente contêm implementações eficientes do Simplex (ou outros algoritmos de otimização). Alguns solvers comuns que a PuLP pode usar são o CBC (que geralmente vem junto ou é fácil de instalar), GLPK, Gurobi ou CPLEX. A PuLP formata seu problema, envia para o solver escolhido, e depois traz a resposta de volta para o seu código Python, informando os valores ótimos para suas variáveis e o resultado da função objetivo.

Em resumo, a PuLP atua como uma ponte inteligente: ela permite que você use a sintaxe familiar do Python para construir seu modelo de otimização, enquanto aproveita o poder de algoritmos complexos como o Simplex implementados em solvers dedicados, sem que você precise se preocupar com os detalhes internos desses algoritmos.

### 1.2. Por que escolhi utilizar o Método Simplex?

A escolha veio de uma combinação de aprendizado e necessidade prática. Essa decisão foi tomada durante as aulas de Pesquisa Operacional na faculdade. Estávamos aprendendo sobre essa específica técnica de otimização, e o Simplex foi apresentado como uma ferramenta poderosa para resolver problemas de programação linear. A professora, então, nos desafiou a aplicar esse método em nossos projetos de pesquisa e inovação que já estavam em andamento.

Meu projeto está ligado à área de empréstimos e crédito. Quando surgiu a necessidade de planejar uma implementação gradual do projeto, percebemos que precisaríamos de bastante capital e, para isso, teríamos que decidir quais modalidades de crédito seriam mais vantajosas para captar ou gerenciar. Tínhamos à disposição diversas opções, como capital de giro, crédito pessoal, financiamento imobiliário, entre outras, cada uma com suas características distintas de taxas de juros, volume de propostas prévias (feitas pelo formulário de captação inicial), capital médio envolvido e até risco de inadimplência.

Foi nesse cenário que o Método Simplex se encaixou perfeitamente. Ele oferecia a estrutura ideal para modelar essa decisão complexa: poderíamos definir um objetivo claro (maximizar o lucro ou retorno esperado dessas operações de crédito) e, ao mesmo tempo, considerar diversas restrições (como limites de capital, capacidade de atendimento ou metas específicas). Basicamente, o Simplex nos permitiria encontrar a "receita" ótima, indicando quanto investir ou focar em cada modalidade de crédito para obter o melhor resultado financeiro possível dentro das regras estabelecidas. Embora o Simplex seja frequentemente associado a problemas de logística ou produção, sua aplicação em um contexto de otimização financeira para negócios pareceu não só adequada, mas também muito pertinente ao desafio que tínhamos em mãos. Por isso, decidimos usá-lo como a ferramenta central para analisar o problema que descreveremos a seguir.

## 2. Problema

### 2.1. Otimizando a Carteira de Crédito Inicial da AutoProvision

Para entender o desafio que vamos analisar com o Método Simplex, vamos primeiro conhecer o cenário e a empresa fictícia criada para este projeto de pesquisa e inovação: a AutoProvision. Imagine a AutoProvision como uma facilitadora, uma ponte entre clientes buscando crédito e instituições financeiras dispostas a emprestar. O objetivo dela é tornar esse processo mais simples e eficiente para todos.

No nosso projeto, simulamos que a AutoProvision realizou um estudo aprofundado do mercado. Para isso, ela combinou dados históricos do setor de crédito (inspirados em informações que poderiam vir de fontes reais como o Banco Central do Brasil, cobrindo o período desde 2012) com os resultados de um formulário próprio, aplicado para medir o interesse de potenciais clientes em diferentes tipos de empréstimo.

Após coletar e organizar essas informações, a AutoProvision chegou a um resumo das principais modalidades de crédito que poderia oferecer, junto com algumas métricas para cada uma delas:
| Modalidade | Propostas Recebidas | Capital Necessário (Média por cliente) | Taxa de Juros (%) | Taxa de Inadimplência (%) |
|------------|---------------------|----------------------------------------|-------------------|---------------------------|
| Capital de Giro | 1400 | 18000 | 2.13 | 0.08 |
| Cheque Especial | 600 | 10000 | 8.16 | 0.07 |
| Crédito Pessoal | 1600 | 15000 | 6.43 | 0.05 |
| Crédito Pessoal Consignado | 6000 | 12500 | 2.36 | 0.10 |
| Financiamento Imobiliário | 3400 | 13425 | 0.81 | 0.15 |
| Aquisição de Veículos | 3400 | 8500 | 1.86 | 0.23 |

| Tabela 1: Resumo das modalidades de crédito e suas características.

Com essa tabela em mãos, surge o problema central: se a AutoProvision decidisse aceitar todas as propostas recebidas de seus potenciais clientes, o montante total de capital necessário para financiar essas operações seria extremamente elevado. Para uma empresa (ainda mais em fase inicial ou de teste), assumir um volume tão grande de operações de uma só vez seria um risco financeiro considerável.

Diante disso, a estratégia mais sensata seria iniciar com uma implementação gradual. Ou seja, começar operando com um volume menor, testar a viabilidade do modelo de negócio, ajustar processos e, só depois, buscar mais investimento para expandir a carteira de clientes. Para essa fase inicial, a AutoProvision precisaria definir limites claros: um número máximo de clientes que poderia atender e um teto para o capital total que poderia alocar.

E é exatamente aqui que a otimização se torna crucial. Tendo essas limitações, como a AutoProvision deveria escolher quais e quantos clientes aceitar de cada modalidade? Simplesmente escolher ao acaso ou focar apenas na modalidade com a maior taxa de juros poderia não ser o ideal, pois há outros fatores em jogo (como o capital exigido por cliente e o risco de inadimplência). Foi essa necessidade – a de encontrar a combinação ótima de empréstimos para maximizar o lucro esperado, dadas as regras de negócio e as restrições de capacidade e capital – que nos levou diretamente à aplicação do Método Simplex. Precisávamos de um algoritmo que nos ajudasse a tomar a melhor decisão possível dentro desse cenário específico.

## 3. Modelo

## 7. Referências

- [Simplex Algorithm - Wikipedia](https://en.wikipedia.org/wiki/Simplex_algorithm).
- [COIN-OR Foundation - Computational Infrastructure for Operations Research](https://www.coin-or.org/).
- [Optimization with PuLP - The PuLP Documentation](https://coin-or.github.io/pulp/).
