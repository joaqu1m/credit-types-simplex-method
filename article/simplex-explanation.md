# Método Simplex

O modelo simplex serve para encontrar a melhor solução em um cenário que você possui algumas diferentes opções. Essa decisão é baseada em valores restritivos, comuns em cada uma das opções, por exemplo:

- Processamento
- Tempo

Em um cenário em que cada uma das opções gasta uma seleta quantidade de processamento e tempo, e o seu problema possui um valor máximo dessas variáveis.

## Valores e Variáveis

Cada uma das opções precisa ter um determinado valor que precisa ser minimizado ou maximizado, por exemplo, uma máquina pode ter:

| Atributo | Valor |
|----------|-------|
| Eficiência | 400 |
| Processamento Necessário | 20 |
| Tempo Gasto | 40 |

> *Detalhe: Os valores não precisam estar devidamente mensurados (sec, min, horas) para que sejam aplicados*
>
> *Os valores PRECISAM ser inteiros para que o simplex funcione, por isso divisões são proibidas na biblioteca* [^1]

[^1]: Com números decimais as possibilidades seriam infinitas.

## Restrições Importantes

**Atenção**: as variáveis que serão otimizadas devem ser únicas. Não pode otimizar as duas, e sim utilizar as duas no cálculo da mesma otimização.

Se você quiser utilizar duas variáveis diferentes para cada mesma opção do modelo, tenha em mente que a primeira é independente da segunda, e só funciona se os dois forem do mesmo tipo (Maximização ou Minimização).

Isso não inclui as RESTRIÇÕES das variáveis. As restrições também devem ser completamente relacionadas.

O exemplo a seguir foi feito com dois diferentes valores restritivos, mas pode considerar de 1 para muitos.

## Exemplo Prático

Vamos supor que esses valores são respectivos ao primeiro mês, e a sua otimização visa encontrar qual a melhor máquina para ficar ligada a cada mês, num período de 12 meses.

Essa máquina tem uma concorrente que gasta mais processamento, mas possui um maior valor de eficiência. O método simplex vai calcular qual a melhor máquina a ser ligada em cada mês. Como não existe uma variável ou restrição que vai fazer com que elas mudem algum valor com o tempo, a máquina escolhida será a mesma todos os meses, mas mesmo assim o modelo foi útil.

## Funcionamento do Modelo

Quando você adiciona valores ao modelo, ao adicionar no sentido de comparação (com `>=`, `<=` ou `==`) eles se tornam _coefficients_, caso contrário se tornam o _objective_.

Quando você insere:
```
problema += 3000 * x1
```

É como se ele salvasse a string '3000 * x1' dentro do modelo para realizar os cálculos (na verdade ele salva um `pulp.LpAffineExpression`, mas pode ser visualizado como aquela string).

```
1.5*x1 = '1.5*x1'
1.5*x1 + 1.5 = '1.5*x1 + 1.5'
1.5*x1 + 1.5*x2 = '1.5*x1 + 1.5*x2'
10*1.5*x1 + 1.5*x2 = '15.0*x1 + 1.5*x2'
1.5*x1*10 + 1.5*x2 = '15.0*x1 + 1.5*x2'
```

## Exemplo Detalhado

### Definindo Restrições

Quando uma restrição é aplicada, por exemplo:

```
modelo += 10000*x1 + 5000*x2 + 20000*x3 <= 20000, 'Capital Total Disponível'
```

A única consideração feita além do valor máximo possível que podemos pegar de cada variável, será o valor atribuído a ela.

### Definindo Objetivos

Vamos supor que o objetivo seja:

```
modelo += x1 + x2 + x3
```

Isso quer dizer que todas as variáveis possuem o mesmo valor.
Neste caso, a variável escolhida será a que permitir maximizar o objetivo dentro das restrições, e o número retornado nessa variável será o próprio valor aplicado ao cálculo. (Ou seja: x2, e não 10_000*x2)

### Diferentes Pesos no Objetivo

Caso os valores tenham pesos diferentes, analisamos qual vale mais a pena escolher, por exemplo:

```
modelo += 2.5*x1 + x2 + x3, 'Total de Clientes Atendidos'
```

Utilizando a mesma restrição de capital, teríamos o resultado:
```
(x1): 2.0
(x2): 0.0
(x3): 0.0
Total de Clientes Atendidos: 5.0
```

### Múltiplas Restrições

Retornando ao objetivo onde todas as variáveis possuem o mesmo valor, vamos aplicar uma segunda restrição (que pode ser por exemplo, de horas diárias de trabalho):

```
modelo += 3*x1 + 7*x2 + 4*x3 <= 12, 'Total de Clientes Processados'
```

Nesse caso, o modelo percebeu que não é mais possível pegar 2 vezes o x2, já que ele vale 7 num contexto em que o máximo é 12. Agora serão feitos outros cálculos, e as outras variáveis agora podem ter mais valor com base na segunda restrição.

Com as restrições isoladas, podemos concluir que os máximos seriam:

**Rest1 (Capital):**
- x1 = 2
- x2 = 4 (Por isso foi escolhida quando isolada, já que é um modelo de maximização)
- x3 = 1

**Rest2 (Processamento):**
- x1 = 4 (em um caso isolado, essa aqui teria sido a escolhida com 4 usos)
- x2 = 1 (e sobram 5 horas)
- x3 = 3

Considerando ambas as restrições simultaneamente, e já que todas as variáveis têm o mesmo valor na função objetivo, o modelo vai buscar a maior quantidade possível de variáveis. Os valores máximos possíveis são:

```
x1 = 1
x2 = 1
x3 = 0
```

ou

```
x1 = 2
x2 = 0
x3 = 0
```

O método `modelo.objective.value()` retorna o resultado da sua operação objetivo após a definição do valor de cada variável.

## Exemplo Final

No exemplo:

```
modelo += 3*x1 + 5*x2 >= 100
```

- x1 = variável
- 3 = coeficiente
- tudo = restrição ou constraint
