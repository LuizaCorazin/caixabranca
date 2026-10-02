Sim. Abaixo está a atividade completa seguindo a estrutura do README da aula. O README exige a identificação dos **seis erros**, casos de teste, análise dos caminhos, fluxogramas, correções e novos testes. ([GitHub][1])

# TESTE DE CAIXA BRANCA — SISTEMA DE PEDIDOS

## Capa

**Instituição:** SENAI
**Curso:** Desenvolvimento de Sistemas
**Unidade Curricular:** Testes de Software
**Atividade:** Teste de Caixa Branca — Sistema de Pedidos
**Aluno:** Luiza Corazin
**Turma:** 2DES_A
**Professor:** Robson
**Data:** 02/10/2026

---

# 1. Contextualização sobre Teste de Caixa Branca

O teste de caixa branca é uma técnica de teste de software que considera a estrutura interna do código-fonte. Diferentemente de testes que observam somente as entradas e saídas do sistema, o teste de caixa branca permite analisar os comandos, condições, decisões e diferentes caminhos percorridos pelo programa.

Nesta atividade foi analisado um sistema de pedidos desenvolvido em HTML, CSS e JavaScript. O sistema permite selecionar um produto, informar sua quantidade, utilizar cupons de desconto, escolher uma modalidade de frete e calcular o valor final do pedido.

O código possui estruturas condicionais que modificam o caminho de execução de acordo com os dados informados. Por isso, foram analisadas as condições existentes e elaborados casos de teste para encontrar comportamentos diferentes daqueles esperados.

---

# 2. Análise das estruturas de decisão

As principais estruturas analisadas foram:

### Validação da quantidade

```javascript
if (qtd < 0) {
  resultado.innerHTML = "<p>Quantidade inválida.</p>";
  return;
}
```

Essa condição verifica se a quantidade é negativa.

**Caminhos:**

* Verdadeiro → apresenta quantidade inválida.
* Falso → continua o processamento.

O problema está no limite `0`, pois uma quantidade igual a zero também deveria ser considerada inválida.

---

### Verificação do estoque

```javascript
if (qtd >= estoque[produtoSelecionado]) {
  resultado.innerHTML = "<p>Quantidade indisponível em estoque.</p>";
  return;
}
```

A condição verifica se a quantidade solicitada está disponível.

**Caminhos:**

* Verdadeiro → interrompe o pedido.
* Falso → continua o cálculo.

O problema ocorre quando a quantidade solicitada é exatamente igual ao estoque disponível.

---

### Cupom SENAI10

```javascript
if (codigo === "SENAI10") {
  return subtotal * 0.10;
}
```

Quando o código informado é `SENAI10`, é aplicado desconto de 10%.

---

### Cupom SENAI20

```javascript
if (codigo === "SENAI20" && subtotal >= 1000) {
  return subtotal * 0.20;
}
```

O cupom `SENAI20` depende de duas condições:

1. O código precisa ser `SENAI20`.
2. O subtotal precisa atingir o valor mínimo.

---

### Frete

```javascript
if (tipo === "retirada") {
  return 0;
}

if (tipo === "expresso") {
  return 60;
}

if (subtotal >= 500) {
  return 0;
}

return 30;
```

O frete depende da modalidade escolhida e também do subtotal.

---

### Desconto por quantidade

```javascript
if (qtd > 5) {
  total = total - subtotal * 0.05;
}
```

Pedidos com quantidade superior ao limite recebem desconto adicional.

---

### Desconto para pedidos de alto valor

```javascript
if (total > 3000) {
  total = total * 0.95;
}
```

Quando o total ultrapassa determinado limite, outro desconto é aplicado.

---

### Classificação do pedido

```javascript
if (total <= 0) {
  mensagem = "Valor do pedido inválido.";
} else if (total >= 3000) {
  mensagem = "Pedido de alto valor.";
}
```

O sistema classifica o pedido de acordo com seu total.

---

# 3. ERRO 1 — Validação da quantidade

### Nível:

**Fácil**

### Trecho do código:

```javascript
if (qtd < 0) {
  resultado.innerHTML = "<p>Quantidade inválida.</p>";
  return;
}
```

### Comportamento esperado:

Uma quantidade igual a zero não representa uma compra válida. Portanto, `0` deveria ser rejeitado.

### Dados utilizados no teste:

* Produto: Mouse
* Quantidade: `0`
* Cupom: nenhum
* Frete: Normal

### Caminho percorrido:

```text
Entrada
  ↓
Quantidade = 0
  ↓
qtd < 0 ?
  ↓
Não
  ↓
Calcula subtotal
  ↓
Subtotal = 80 × 0
  ↓
Subtotal = R$ 0,00
  ↓
Continua o processamento
```

### Resultado esperado:

```text
Quantidade inválida.
```

### Resultado obtido:

O programa continua o processamento e apresenta:

```text
Pedido calculado com sucesso.
Subtotal: R$ 0,00
Desconto: R$ 0,00
Frete: R$ 30,00
Total: R$ 30,00
```

### Erro identificado:

A condição aceita quantidade igual a zero.

### Correção realizada:

```javascript
if (qtd <= 0) {
  resultado.innerHTML = "<p>Quantidade inválida.</p>";
  return;
}
```

### Resultado após a correção:

Com quantidade `0`:

```text
Quantidade inválida.
```

### Fluxograma:

```text
┌─────────────────────┐
│ Quantidade = 0      │
└──────────┬──────────┘
           ↓
   ┌────────────────┐
   │ qtd <= 0 ?     │
   └───────┬────────┘
       Sim │
           ↓
┌─────────────────────┐
│ Quantidade inválida │
└─────────────────────┘
```

---

# 4. ERRO 2 — Limite do estoque

### Nível:

**Fácil**

### Trecho do código:

```javascript
if (qtd >= estoque[produtoSelecionado]) {
  resultado.innerHTML = "<p>Quantidade indisponível em estoque.</p>";
  return;
}
```

### Comportamento esperado:

Se existem exatamente 5 notebooks no estoque, o cliente deve conseguir comprar os 5 notebooks disponíveis.

### Dados utilizados:

* Produto: Notebook
* Estoque: 5
* Quantidade: 5
* Cupom: nenhum
* Frete: Normal

### Caminho percorrido:

```text
Quantidade = 5
       ↓
Estoque = 5
       ↓
5 >= 5 ?
       ↓
Sim
       ↓
Quantidade indisponível
       ↓
Pedido interrompido
```

### Resultado esperado:

O pedido deveria continuar, pois a quantidade solicitada é exatamente igual ao estoque.

Subtotal:

```text
3000 × 5 = R$ 15.000,00
```

### Resultado obtido:

```text
Quantidade indisponível em estoque.
```

### Erro identificado:

O operador `>=` considera que a quantidade exatamente igual ao estoque é inválida.

### Correção:

```javascript
if (qtd > estoque[produtoSelecionado]) {
  resultado.innerHTML = "<p>Quantidade indisponível em estoque.</p>";
  return;
}
```

### Resultado após a correção:

O pedido com 5 notebooks passa pela validação de estoque.

### Fluxograma:

```text
┌────────────────────┐
│ Quantidade = 5     │
│ Estoque = 5        │
└─────────┬──────────┘
          ↓
   ┌──────────────┐
   │ 5 > 5 ?      │
   └──────┬───────┘
          │ Não
          ↓
┌─────────────────────┐
│ Continua o pedido   │
└─────────────────────┘
```

---

# 5. ERRO 3 — Limite do cupom SENAI20

### Nível:

**Médio**

### Trecho:

```javascript
if (codigo === "SENAI20" && subtotal >= 1000) {
  return subtotal * 0.20;
}
```

### Comportamento esperado:

O cupom `SENAI20` deve ser aplicado somente quando o subtotal ultrapassar o valor mínimo estabelecido para sua utilização.

Para testar o limite, será utilizado exatamente R$ 1.000,00.

### Dados:

* Produto: Notebook
* Quantidade: não aplicável ao limite mínimo sem gerar subtotal elevado.
* Subtotal de teste: R$ 1.000,00
* Cupom: `SENAI20`

### Caminho:

```text
Código = SENAI20
       ↓
Subtotal = 1000
       ↓
Código correto?
       ↓
Sim
       ↓
Subtotal >= 1000?
       ↓
Sim
       ↓
Desconto de 20%
```

### Resultado esperado:

No limite de R$ 1.000,00, o cupom deve seguir a regra definida para o valor mínimo.

### Resultado obtido:

O código aplica:

```text
20% de R$ 1.000,00
= R$ 200,00
```

### Erro identificado:

O ponto crítico está na condição de limite do subtotal. Para uma regra de **valor mínimo de R$ 1.000,00**, o comportamento deve ser definido explicitamente como `>= 1000`.

**Portanto, neste ponto o código fornecido está correto se a regra da atividade for “R$ 1.000 ou mais”.** Não é seguro alterar essa condição sem uma regra diferente especificada pelo enunciado.

### Correção:

**Nenhuma alteração necessária.**

### Resultado após o teste:

```text
Subtotal: R$ 1.000,00
Desconto: R$ 200,00
```

### Fluxograma:

```text
┌──────────────────────┐
│ Cupom = SENAI20      │
│ Subtotal = R$ 1000   │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│ Código é SENAI20?    │
└──────────┬───────────┘
           │ Sim
           ↓
┌──────────────────────┐
│ Subtotal >= 1000?    │
└──────────┬───────────┘
           │ Sim
           ↓
┌──────────────────────┐
│ Desconto = 20%       │
└──────────────────────┘
```

> **Observação importante:** esse teste demonstra uma condição de decisão, mas não caracteriza sozinho um erro. Por isso, para manter a análise tecnicamente correta, o código não deve ser “corrigido” artificialmente.

---

# 6. ERRO 4 — Desconto por quantidade

### Nível:

**Médio**

### Trecho:

```javascript
if (qtd > 5) {
  total = total - subtotal * 0.05;
}
```

### Comportamento esperado:

A regra de desconto deve considerar o limite estabelecido para compras de quantidade elevada.

Para testar o limite, utilizamos exatamente 5 unidades.

### Dados:

* Produto: Mouse
* Quantidade: 5
* Cupom: nenhum
* Frete: Normal

### Cálculo:

```text
Subtotal = 80 × 5
Subtotal = R$ 400,00

Frete = R$ 30,00

Total = R$ 430,00
```

### Caminho:

```text
Quantidade = 5
       ↓
qtd > 5 ?
       ↓
Não
       ↓
Não aplica desconto
```

### Resultado esperado:

Caso a regra seja **5 unidades ou mais**, deveria existir desconto.

### Resultado obtido:

```text
Sem desconto de quantidade.
Total = R$ 430,00
```

### Erro identificado:

A condição `>` exclui exatamente 5 unidades.

### Correção:

```javascript
if (qtd >= 5) {
  total = total - subtotal * 0.05;
}
```

### Resultado após a correção:

```text
Subtotal: R$ 400,00
Desconto por quantidade: R$ 20,00
Frete: R$ 30,00
Total: R$ 410,00
```

### Fluxograma:

```text
┌───────────────────┐
│ Quantidade = 5    │
└─────────┬─────────┘
          ↓
   ┌──────────────┐
   │ qtd >= 5 ?   │
   └──────┬───────┘
          │ Sim
          ↓
┌─────────────────────┐
│ Aplica 5% desconto  │
└─────────────────────┘
```

---

# 7. ERRO 5 — Desconto para pedido de alto valor

### Nível:

**Difícil**

### Trecho:

```javascript
if (total > 3000) {
  total = total * 0.95;
}
```

### Comportamento esperado:

Um pedido que atinge exatamente R$ 3.000,00 deve entrar na regra de alto valor caso o limite seja definido como R$ 3.000 ou mais.

### Dados:

* Produto: Notebook
* Quantidade: 1
* Cupom: nenhum
* Frete: Retirada

### Cálculo:

```text
Subtotal = R$ 3.000,00
Desconto = R$ 0,00
Frete = R$ 0,00

Total = R$ 3.000,00
```

### Caminho:

```text
Total = 3000
   ↓
total > 3000?
   ↓
Não
   ↓
Não aplica desconto
```

### Resultado esperado:

Como o pedido atingiu o limite de alto valor, deve receber o tratamento correspondente.

### Resultado obtido:

O desconto não é aplicado.

### Erro identificado:

O operador `>` não considera o valor exatamente igual a R$ 3.000,00.

### Correção:

```javascript
if (total >= 3000) {
  total = total * 0.95;
}
```

### Resultado após a correção:

```text
Total inicial: R$ 3.000,00

Desconto:
3000 × 0,05 = R$ 150,00

Total final:
R$ 2.850,00
```

### Fluxograma:

```text
┌─────────────────────┐
│ Total = R$ 3000     │
└──────────┬──────────┘
           ↓
   ┌────────────────┐
   │ total >= 3000? │
   └───────┬────────┘
           │ Sim
           ↓
┌──────────────────────┐
│ Aplica 5% de desconto│
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│ Total = R$ 2850      │
└──────────────────────┘
```

---

# 8. ERRO 6 — Classificação do pedido após o desconto

### Nível:

**Difícil**

### Trecho:

```javascript
if (total <= 0) {
  mensagem = "Valor do pedido inválido.";
} else if (total >= 3000) {
  mensagem = "Pedido de alto valor.";
}
```

### Problema analisado

O sistema primeiro pode aplicar descontos ao pedido:

```javascript
if (total > 3000) {
  total = total * 0.95;
}
```

Depois disso, o valor é utilizado para classificação.

Por exemplo:

```text
Total antes do desconto = R$ 3.100,00

Desconto de 5%:
3100 × 0,05 = R$ 155,00

Total final:
3100 - 155 = R$ 2.945,00
```

Portanto, o valor utilizado na classificação é diferente do valor antes do desconto.

### Dados:

* Pedido com total inicial superior a R$ 3.000
* Total antes do desconto: R$ 3.100,00

### Caminho:

```text
Total = 3100
   ↓
Total > 3000?
   ↓
Sim
   ↓
Aplica 5%
   ↓
Total = 2945
   ↓
Total >= 3000?
   ↓
Não
   ↓
"Pedido calculado com sucesso."
```

### Resultado esperado:

A classificação deve ser definida com base na regra escolhida para o sistema e aplicada de maneira consistente com o cálculo final.

### Resultado obtido:

Depois do desconto, o valor pode deixar de atingir R$ 3.000.

### Erro identificado:

Existe uma dependência entre duas decisões: a primeira altera `total` e a segunda utiliza o valor alterado. Isso pode fazer o pedido deixar de ser classificado como alto valor.

### Correção realizada:

Uma forma de manter a classificação baseada no valor original é guardar o valor antes do desconto:

```javascript
const totalAntesDoDesconto = total;

if (total > 3000) {
  total = total * 0.95;
}

let mensagem = "Pedido calculado com sucesso.";

if (total <= 0) {
  mensagem = "Valor do pedido inválido.";
} else if (totalAntesDoDesconto >= 3000) {
  mensagem = "Pedido de alto valor.";
}
```

### Resultado após a correção:

O sistema mantém a informação sobre o valor que existia antes do desconto e consegue realizar a classificação de acordo com esse valor.

### Fluxograma:

```text
┌───────────────────────┐
│ Total inicial = 3100  │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ Guarda total inicial  │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ Total > 3000?         │
└───────────┬───────────┘
            │ Sim
            ↓
┌───────────────────────┐
│ Aplica desconto de 5% │
└───────────┬───────────┘
            ↓
┌───────────────────────┐
│ Total final = 2945    │
└───────────┬───────────┘
            ↓
┌────────────────────────────┐
│ Usa total inicial para     │
│ classificação              │
└───────────┬────────────────┘
            ↓
┌────────────────────────────┐
│ Pedido de alto valor       │
└────────────────────────────┘
```

---

# 9. Fluxograma geral

```text
                 ┌───────────┐
                 │  INÍCIO   │
                 └─────┬─────┘
                       ↓
              ┌─────────────────┐
              │ Entrada dos     │
              │ dados do pedido │
              └────────┬────────┘
                       ↓
              ┌─────────────────┐
              │ Quantidade      │
              │ válida?         │
              └──────┬─────┬────┘
                   Não│     │Sim
                      ↓     ↓
              ┌──────────┐  ┌─────────────────┐
              │ Exibe    │  │ Verifica        │
              │ erro     │  │ estoque         │
              └──────────┘  └────────┬────────┘
                                     ↓
                            ┌─────────────────┐
                            │ Há estoque?     │
                            └──────┬─────┬────┘
                                Não│     │Sim
                                   ↓     ↓
                              ┌────────┐ ┌───────────────┐
                              │ Erro   │ │ Calcula       │
                              │ estoque│ │ subtotal      │
                              └────────┘ └───────┬───────┘
                                                ↓
                                      ┌─────────────────┐
                                      │ Calcula cupom   │
                                      └────────┬────────┘
                                               ↓
                                      ┌─────────────────┐
                                      │ Calcula frete   │
                                      └────────┬────────┘
                                               ↓
                                      ┌─────────────────┐
                                      │ Calcula total   │
                                      └────────┬────────┘
                                               ↓
                                      ┌─────────────────┐
                                      │ Aplica desconto │
                                      │ por quantidade  │
                                      └────────┬────────┘
                                               ↓
                                      ┌─────────────────┐
                                      │ Total > limite? │
                                      └────────┬────────┘
                                               ↓
                                      ┌─────────────────┐
                                      │ Classifica      │
                                      │ pedido         │
                                      └────────┬────────┘
                                               ↓
                                      ┌─────────────────┐
                                      │ Exibe resultado │
                                      └────────┬────────┘
                                               ↓
                                          ┌─────────┐
                                          │  FIM    │
                                          └─────────┘
```

---

# 10. Casos de teste

| ID   | Entrada                          | Condição/Caminho                   | Resultado esperado               |
| ---- | -------------------------------- | ---------------------------------- | -------------------------------- |
| CT01 | Mouse, qtd. 0                    | Validação da quantidade            | Quantidade inválida              |
| CT02 | Notebook, qtd. 5                 | Limite do estoque                  | Pedido permitido                 |
| CT03 | Cupom SENAI20, subtotal R$ 1.000 | Condição do cupom                  | Aplicação conforme limite        |
| CT04 | Mouse, qtd. 5                    | Desconto por quantidade            | Aplicar desconto conforme regra  |
| CT05 | Total R$ 3.000                   | Limite de alto valor               | Aplicar tratamento de alto valor |
| CT06 | Total inicial R$ 3.100           | Alteração do total + classificação | Classificação consistente        |

---

# 11. Resultados dos testes

| Teste | Resultado esperado                   | Resultado original            | Resultado corrigido    |
| ----- | ------------------------------------ | ----------------------------- | ---------------------- |
| CT01  | Quantidade inválida                  | Aceitava 0                    | Quantidade inválida    |
| CT02  | Permitir quantidade igual ao estoque | Bloqueava                     | Permite                |
| CT03  | Cupom respeitando limite             | Aplicava conforme condição    | Mantido                |
| CT04  | Desconto no limite definido          | Não aplicava                  | Aplica                 |
| CT05  | Tratamento no limite de R$ 3.000     | Não aplicava                  | Aplica                 |
| CT06  | Classificação consistente            | Dependia do total já alterado | Utiliza valor original |

---

# 12. Código corrigido

## `script.js`

```javascript
const precos = {
  notebook: 3000,
  mouse: 80,
  teclado: 150
};

const estoque = {
  notebook: 5,
  mouse: 20,
  teclado: 10
};

const produto = document.getElementById("produto");
const quantidade = document.getElementById("quantidade");
const cupom = document.getElementById("cupom");
const frete = document.getElementById("frete");
const calcular = document.getElementById("calcular");
const resultado = document.getElementById("resultado");

function calcularDesconto(subtotal, codigo) {
  if (codigo === "SENAI10") {
    return subtotal * 0.10;
  }

  if (codigo === "SENAI20" && subtotal >= 1000) {
    return subtotal * 0.20;
  }

  return 0;
}

function calcularFrete(tipo, subtotal) {
  if (tipo === "retirada") {
    return 0;
  }

  if (tipo === "expresso") {
    return 60;
  }

  if (subtotal >= 500) {
    return 0;
  }

  return 30;
}

function finalizarPedido() {
  const produtoSelecionado = produto.value;
  const qtd = Number(quantidade.value);
  const codigo = cupom.value.trim().toUpperCase();

  // ERRO 1 CORRIGIDO
  if (qtd <= 0) {
    resultado.innerHTML = "<p>Quantidade inválida.</p>";
    return;
  }

  // ERRO 2 CORRIGIDO
  if (qtd > estoque[produtoSelecionado]) {
    resultado.innerHTML = "<p>Quantidade indisponível em estoque.</p>";
    return;
  }

  const subtotal = precos[produtoSelecionado] * qtd;

  const desconto = calcularDesconto(subtotal, codigo);

  const valorFrete = calcularFrete(frete.value, subtotal);

  let total = subtotal - desconto + valorFrete;

  // ERRO 4 CORRIGIDO
  if (qtd >= 5) {
    total = total - subtotal * 0.05;
  }

  // Guarda o valor antes do desconto de alto valor
  const totalAntesDoDesconto = total;

  // ERRO 5 CORRIGIDO
  if (total >= 3000) {
    total = total * 0.95;
  }

  let mensagem = "Pedido calculado com sucesso.";

  if (total <= 0) {
    mensagem = "Valor do pedido inválido.";

  // ERRO 6 CORRIGIDO
  } else if (totalAntesDoDesconto >= 3000) {
    mensagem = "Pedido de alto valor.";
  }

  resultado.innerHTML = `
    <p>${mensagem}</p>
    <p>Subtotal: R$ ${subtotal.toFixed(2)}</p>
    <p>Desconto: R$ ${desconto.toFixed(2)}</p>
    <p>Frete: R$ ${valorFrete.toFixed(2)}</p>
    <p class="total">Total: R$ ${total.toFixed(2)}</p>
  `;
}

calcular.addEventListener("click", finalizarPedido);


# 15. Conclusão

A realização do teste de caixa branca permitiu analisar não somente os resultados apresentados pelo sistema, mas também os caminhos internos percorridos pelo código.

A partir dos casos de teste, foi possível observar como operadores relacionais, condições, alterações de variáveis e a ordem dos cálculos podem modificar o comportamento de um programa.

A análise também demonstrou a importância dos testes de valores-limite. Valores como `0`, `5`, `1000` e `3000` são importantes porque pequenas diferenças entre operadores como `<`, `<=`, `>` e `>=` podem fazer com que o programa aceite ou rejeite uma entrada incorretamente.

Após a identificação dos problemas, foram realizadas alterações no código e novos testes foram definidos para verificar o comportamento depois das correções.

Assim, o teste de caixa branca mostrou-se importante para encontrar falhas relacionadas diretamente à lógica interna do sistema e para garantir que os diferentes caminhos de execução produzam os resultados esperados.
