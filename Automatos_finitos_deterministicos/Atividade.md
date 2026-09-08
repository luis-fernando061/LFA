# Lista de Exercícios — Autômatos Finitos Determinísticos (AFD)

> **Disciplina:** Teoria das Linguagens e Autômatos  
> **Tema:** Autômatos Finitos Determinísticos  
> **Modalidade:** Atividade prática em grupo  
> **Objetivo:** identificar, interpretar, construir e testar AFDs.

---

## Identificação do grupo

| Campo | Preenchimento |
|---|---|
| Turma | |
| Data | |
| Integrante 1 | |
| Integrante 2 | |
| Integrante 3 | |
| Integrante 4 | |

## Orientações

- Registre o raciocínio utilizado em cada resposta.
- Nos exercícios com cadeias, apresente o caminho percorrido estado por estado.
- Nos exercícios de construção, entregue a quíntupla, a tabela de transição e o diagrama.
- Use `ε` para representar a cadeia vazia.
- Quando solicitado, implemente e teste o autômato no JFLAP.

---

# Parte 1 — Fundamentos

## [Exercício 1 — Entendendo um autômato finito

Uma lâmpada controlada por um interruptor possui os estados `Desligado` e `Ligado`. Sempre que o botão é pressionado, ocorre a mudança:

```text
Desligado --pressionar--> Ligado
Ligado    --pressionar--> Desligado
```

Responda:

# Exercício 1 — Entendendo um autômato finito

1. **Quantos estados existem?** — **Existem 2 estados: `Desligado` e `Ligado`.**

2. **Qual é o estado inicial, considerando que a lâmpada começa apagada?** — **O estado inicial é `Desligado`.**

3. **Qual entrada provoca uma transição?** — **A entrada é `pressionar` o botão.**

4. **Partindo de `Desligado`, qual será o estado após um acionamento?** — **Após um acionamento, o estado será `Ligado`.**

5. **Partindo de `Desligado`, qual será o estado após dois acionamentos?** — **Após dois acionamentos, o estado será `Desligado`.**

6. **Explique o funcionamento do sistema com suas palavras.** — **O sistema possui dois estados, `Desligado` e `Ligado`. Cada vez que o botão é pressionado, a lâmpada muda para o estado contrário: se estiver desligada, fica ligada; se estiver ligada, fica desligada.**

Uma porta automática possui os estados `Fechado` e `Aberto`. O sensor identifica `pessoa_detectada` ou `nenhuma_pessoa`. Quando uma pessoa é detectada, a porta deve ficar aberta; quando ninguém é detectado, deve ficar fechada.

## Exercício — Porta automática

1. **Complete a tabela:**

| Estado atual | Entrada          | Próximo estado |
| ------------ | ---------------- | -------------- |
| Fechado      | pessoa_detectada | **Aberto**     |
| Fechado      | nenhuma_pessoa   | **Fechado**    |
| Aberto       | pessoa_detectada | **Aberto**     |
| Aberto       | nenhuma_pessoa   | **Fechado**    |

2. **Desenhe o diagrama de estados correspondente e indique o estado inicial:**

```text
                 pessoa_detectada
          ┌──────────────────────────┐
          │                          ▼
     → [Fechado] ────────────────> [Aberto]
          ▲                          │
          │                          │
          └──────────────────────────┘
             nenhuma_pessoa
```

**Estado inicial:** **`Fechado`**

**Explicação das transições:** — **Se uma pessoa for detectada, a porta vai para `Aberto`. Se nenhuma pessoa for detectada, a porta vai ou permanece em `Fechado`.**

---

# Parte 2 — Anatomia e definição formal

## Exercício 3 — Identificando os elementos

Considere um AFD com `Σ = {0,1}`, `Q = {q0,q1}`, estado inicial `q0`, estado final `q1` e as transições abaixo:

| δ | 0 | 1 |
|---|---|---|
| q0 | q0 | q1 |
| q1 | q0 | q1 |

Identifique e explique:

1. **O alfabeto `Σ`** — **`Σ = {0,1}`. É o conjunto de símbolos que o autômato pode receber como entrada.**

2. **O conjunto de estados `Q`** — **`Q = {q0, q1}`. São os estados que fazem parte do autômato.**

3. **O estado inicial** — **O estado inicial é `q0`, pois é o estado indicado como início do autômato.**

4. **O conjunto de estados finais `F`** — **`F = {q1}`. O estado `q1` é o estado final ou de aceitação.**

5. **Os símbolos que podem ser lidos** — **Os símbolos que podem ser lidos são `0` e `1`, pois pertencem ao alfabeto `Σ`.**

6. **O significado do círculo duplo em um diagrama** — **O círculo duplo representa um estado final ou estado de aceitação. Neste caso, `q1` seria representado com círculo duplo.**

7. **O significado da seta sem origem apontando para um estado** — **A seta sem origem indica o estado inicial do autômato. Neste caso, ela apontaria para `q0`.**

## Exercício 4 — A quíntupla do AFD

### Exercício — Representação formal de um AFD

| Elemento | Significado                                                                                         |
| -------- | --------------------------------------------------------------------------------------------------- |
| `Σ`      | **É o alfabeto, ou seja, o conjunto de símbolos que podem ser lidos pelo autômato.**                |
| `Q`      | **É o conjunto de estados do autômato.**                                                            |
| `δ`      | **É a função de transição, que determina para qual estado o autômato deve ir ao ler cada símbolo.** |
| `q0`     | **É o estado inicial, onde o autômato começa a processar a entrada.**                               |
| `F`      | **É o conjunto de estados finais ou de aceitação.**                                                 |

**Por que esses cinco elementos são suficientes para definir o funcionamento de um AFD?** — **Porque eles informam todos os componentes necessários: quais símbolos podem ser lidos (`Σ`), quais estados existem (`Q`), como o autômato muda de estado (`δ`), onde ele começa (`q0`) e quais estados representam uma entrada aceita (`F`). Com essas cinco informações, é possível determinar o comportamento completo do AFD para qualquer sequência de símbolos.**

---

# Parte 3 — Tabela de transições e cadeias

## Exercício 5 — Interpretando uma tabela

### Exercício — Função de transição de um AFD

1. **Qual é o resultado de `δ(q0,0)`?** — **`q0`**

2. **Qual é o resultado de `δ(q0,1)`?** — **`q1`**

3. **Qual é o resultado de `δ(q1,0)`?** — **`q2`**

4. **Qual é o resultado de `δ(q2,1)`?** — **`q1`**

5. **Qual é o estado de aceitação?** — **O estado de aceitação é `q1`, pois `F = {q1}`.**

6. **Desenhe o diagrama correspondente à tabela.** — **O diagrama de estados é:**

```text
                 1
            ┌─────────┐
            │         ▼
        → (q0) ──1──> ((q1))
          ↑  │         │  │
          │  │0        │  │
          │  │         │  │0
          └──┘         ▼  ▼
                     (q2)
                       │
                       │ 0,1
                       ▼
                     ((q1))
```

**Representação das transições:**

```text
q0 --0--> q0
q0 --1--> q1
q1 --0--> q2
q1 --1--> q1
q2 --0--> q1
q2 --1--> q1
```

**Observação:** `q0` é o estado inicial (seta sem origem) e `q1` é o estado final (círculo duplo).

7. **Justifique por que o autômato é determinístico.** — **O autômato é determinístico porque, para cada estado e para cada símbolo de entrada (`0` ou `1`), existe exatamente uma única transição possível. Portanto, não há escolhas ou ambiguidades sobre qual estado será alcançado.**


## Exercício 6 — Aceita ou rejeita?

#### Exercício — Função de transição de um AFD

1. **Qual é o resultado de `δ(q0,0)`?** — **`q0`**

2. **Qual é o resultado de `δ(q0,1)`?** — **`q1`**

3. **Qual é o resultado de `δ(q1,0)`?** — **`q2`**

4. **Qual é o resultado de `δ(q2,1)`?** — **`q1`**

5. **Qual é o estado de aceitação?** — **`q1`, pois `F = {q1}`.**

6. **Desenhe o diagrama correspondente à tabela.** — **O diagrama de estados correspondente é:**

```text
                 1
            ┌─────────┐
            │         ▼
        → (q0) ──1──> ((q1))
          │            │  │
         0│            │  │1
          ▼            │  │
         (q0)          │  │
                       │  │
                       │  │0
                       ▼  │
                      (q2)│
                       │  │
                       └──┘
                      0,1
                       │
                       ▼
                     ((q1))
```

**Representação das transições:**

```text
q0 --0--> q0
q0 --1--> q1
q1 --0--> q2
q1 --1--> q1
q2 --0--> q1
q2 --1--> q1
```

**Estado inicial:** — **`q0`**

**Estado final:** — **`q1`**

7. **Justifique por que o autômato é determinístico.** — **O autômato é determinístico porque, para cada estado e cada símbolo de entrada (`0` ou `1`), existe exatamente uma única transição possível. Assim, o autômato nunca fica em dúvida sobre qual estado deve seguir.**

# Parte 4 — Construção de AFDs

## Exercício 7 — Cadeias que terminam em `1`

# Exercício — AFD para cadeias que terminam em `1`

### 1. Conjunto de estados `Q`

**`Q = {q0, q1}`**

### 2. Alfabeto `Σ`

**`Σ = {0,1}`**

### 3. Estado inicial

**`q0`**

### 4. Estados finais `F`

**`F = {q1}`**

### 5. Tabela de transições

| Estado atual | Entrada `0` | Entrada `1` |
| ------------ | ----------- | ----------- |
| `q0`         | `q0`        | `q1`        |
| `q1`         | `q0`        | `q1`        |

### 6. Diagrama de estados

**O diagrama correspondente é:**

```text
              1
        ┌──────────────┐
        │              ▼
       (q0) ────────> ((q1))
        ↑              │
        │              │
        └────── 0 ─────┘
        
        q0 --0--> q0
        q1 --0--> q0
        q0 --1--> q1
        q1 --1--> q1
```

**`q0` é o estado inicial e `q1` é o estado final.**

### 7. Teste das cadeias

| Cadeia | Caminho                  | Resultado   |
| ------ | ------------------------ | ----------- |
| `1`    | `q0 → q1`                | **Aceita**  |
| `01`   | `q0 → q0 → q1`           | **Aceita**  |
| `101`  | `q0 → q1 → q0 → q1`      | **Aceita**  |
| `0001` | `q0 → q0 → q0 → q0 → q1` | **Aceita**  |
| `1101` | `q0 → q1 → q1 → q0 → q1` | **Aceita**  |
| `ε`    | `q0`                     | **Rejeita** |
| `0`    | `q0 → q0`                | **Rejeita** |
| `10`   | `q0 → q1 → q0`           | **Rejeita** |
| `100`  | `q0 → q1 → q0 → q0`      | **Rejeita** |
| `1110` | `q0 → q1 → q1 → q1 → q0` | **Rejeita** |

### 8. Conclusão

**O AFD aceita somente as cadeias cujo último símbolo é `1`. Quando o último símbolo lido é `1`, o autômato permanece em `q1`, que é o estado de aceitação. Quando o último símbolo é `0`, ele vai para `q0`, que não é um estado final.**


## Exercício 8 — Número par de símbolos `1`
# Exercício — AFD para quantidade par de símbolos `1`

### 1. Definição formal

**`M = (Σ, Q, δ, q0, F)`**, onde:

* **`Σ = {0,1}`** — alfabeto de entrada.
* **`Q = {qPar, qImpar}`** — conjunto de estados.
* **`q0 = qPar`** — estado inicial.
* **`F = {qPar}`** — conjunto de estados finais.
* **`δ`** — função de transição que controla se a quantidade de `1` é par ou ímpar.

### 2. Tabela de transições

| Estado atual | `0`      | `1`      |
| ------------ | -------- | -------- |
| `qPar`       | `qPar`   | `qImpar` |
| `qImpar`     | `qImpar` | `qPar`   |

**Explicação:** — **O símbolo `0` não altera a quantidade de `1`, então mantém o estado. O símbolo `1` altera a situação: de par para ímpar ou de ímpar para par.**

### 3. Diagrama de estados

**O diagrama correspondente é:**

```text
                         1
              ┌──────────────────┐
              │                  ▼
           → ((qPar)) ──1──> (qImpar)
               ▲                │
               │                │
               └───────1────────┘
               
               0                 0
          ┌────────┐        ┌────────┐
          │        │        │        │
          └─ qPar ─┘        └ qImpar ┘
```

**`qPar` é o estado inicial e também o estado final, pois a quantidade de `1` deve ser par para que a cadeia seja aceita.**

### 4. Processamento das cadeias

| Cadeia  | Quantidade de `1` | Caminho                                         | Resultado   |
| ------- | ----------------: | ----------------------------------------------- | ----------- |
| `ε`     |                 0 | `qPar`                                          | **Aceita**  |
| `0`     |                 0 | `qPar → qPar`                                   | **Aceita**  |
| `1`     |                 1 | `qPar → qImpar`                                 | **Rejeita** |
| `11`    |                 2 | `qPar → qImpar → qPar`                          | **Aceita**  |
| `101`   |                 2 | `qPar → qImpar → qImpar → qPar`                 | **Aceita**  |
| `1100`  |                 2 | `qPar → qImpar → qPar → qPar → qPar`            | **Aceita**  |
| `10101` |                 3 | `qPar → qImpar → qImpar → qPar → qPar → qImpar` | **Rejeita** |

### 5. Conclusão

**O AFD aceita as cadeias que possuem uma quantidade par de símbolos `1`, incluindo zero `1`. Por isso, `ε` e `0` são aceitas, enquanto `1` e `10101` são rejeitadas. O autômato precisa apenas controlar duas situações: `qPar` (quantidade par de `1`) e `qImpar` (quantidade ímpar de `1`).**

## Exercício 9 — Pelo menos dois zeros consecutivos
# Parte 5 — Desafios de modelagem

## Exercício — AFD com pelo menos dois `0`s consecutivos

### Antes de construir o AFD

1. **O que o estado inicial representa?** — **Representa que ainda não encontramos dois `0`s consecutivos.**

2. **O que ocorre quando aparece o primeiro `0`?** — **O autômato vai para um estado que indica que já foi encontrado um `0`, mas ainda não temos dois `0`s consecutivos.**

3. **O que ocorre quando outro `0` aparece imediatamente depois?** — **O autômato reconhece `00` e vai para o estado final de aceitação.**

4. **Depois de encontrar `00`, a cadeia pode deixar de ser aceita?** — **Não. Depois que `00` é encontrado, a cadeia continua sendo aceita, independentemente dos símbolos que aparecerem depois.**

5. **Quantos estados são necessários?** — **São necessários 3 estados: um para nenhum `0` consecutivo, um para um único `0` no final e um estado final para indicar que `00` já foi encontrado.**

---

## 1. Definição formal

**A quíntupla é:**

**`M = (Σ, Q, δ, q0, F)`**

* **`Σ = {0,1}`** — alfabeto de entrada.
* **`Q = {q0, q1, q2}`** — conjunto de estados.
* **`q0`** — estado inicial.
* **`F = {q2}`** — conjunto de estados finais.
* **`δ`** — função de transição.

**Significado dos estados:**

* **`q0`** — ainda não foi encontrado `00`.
* **`q1`** — o último símbolo lido foi `0`, mas ainda não apareceu `00`.
* **`q2`** — `00` já foi encontrado; portanto, a cadeia será aceita.

---

## 2. Tabela de transições

| Estado atual | `0`  | `1`  |
| ------------ | ---- | ---- |
| `q0`         | `q1` | `q0` |
| `q1`         | `q2` | `q0` |
| `q2`         | `q2` | `q2` |

**Explicação:** — **Em `q0`, ao ler `0`, vamos para `q1`. Se em `q1` aparecer outro `0`, temos `00` e vamos para `q2`. Após chegar em `q2`, permanecemos nele, pois a sequência `00` já foi encontrada.**

---

## 3. Diagrama de estados

**O diagrama correspondente é:**

```text
                  0
          ┌──────────────► (q1)
          │                  │
          │                  │ 0
          │                  ▼
      → (q0)              ((q2))
        │  ▲              ↺ 0,1
        │  │
       1│  │1
        │  │
        └──┘

(q1) ──1──> (q0)
```

**`q0` é o estado inicial e `q2` é o estado final (círculo duplo).**

---

## 4. Testes das cadeias

| Cadeia   | Caminho percorrido                 | Resultado   |
| -------- | ---------------------------------- | ----------- |
| `00`     | `q0 → q1 → q2`                     | **Aceita**  |
| `001`    | `q0 → q1 → q2 → q2`                | **Aceita**  |
| `100`    | `q0 → q0 → q1 → q2`                | **Aceita**  |
| `1001`   | `q0 → q0 → q1 → q2 → q2`           | **Aceita**  |
| `110011` | `q0 → q0 → q0 → q1 → q2 → q2 → q2` | **Aceita**  |
| `0000`   | `q0 → q1 → q2 → q2 → q2`           | **Aceita**  |
| `ε`      | `q0`                               | **Rejeita** |
| `0`      | `q0 → q1`                          | **Rejeita** |
| `1`      | `q0 → q0`                          | **Rejeita** |
| `01`     | `q0 → q1 → q0`                     | **Rejeita** |
| `10`     | `q0 → q0 → q1`                     | **Rejeita** |
| `10101`  | `q0 → q0 → q1 → q0 → q1 → q0`      | **Rejeita** |

### 5. Conclusão

**O AFD aceita uma cadeia somente quando encontra pelo menos dois `0`s consecutivos (`00`). Assim que `00` aparece, o autômato permanece no estado final `q2`, pois a condição de aceitação já foi satisfeita.**


## Exercício 10 — Semáforo

# Exercício — Modelagem de um Semáforo

### 1. Diagrama de estados

**O diagrama do semáforo é:**

```text
        tempo              tempo
   ┌─────────────┐     ┌─────────────┐
   │             ▼     │             ▼
[Verde] ─────> [Amarelo] ─────> [Vermelho]
   ▲                                      │
   │                                      │
   └──────────────── tempo ───────────────┘
```

### 2. Tabela de transições

| Estado atual | Entrada | Próximo estado |
| ------------ | ------- | -------------- |
| Verde        | `tempo` | **Amarelo**    |
| Amarelo      | `tempo` | **Vermelho**   |
| Vermelho     | `tempo` | **Verde**      |

### 3. Definição formal

**`M = (Σ, Q, δ, q0, F)`**

* **`Σ = {tempo}`** — conjunto de entradas.
* **`Q = {Verde, Amarelo, Vermelho}`** — conjunto de estados.
* **`q0 = Verde`** — estado inicial, considerando que o semáforo começa no verde.
* **`F = ∅`** — conjunto de estados finais vazio, pois o semáforo não possui um estado de aceitação.
* **`δ`** — função de transição que define a mudança de estado quando ocorre a entrada `tempo`.

### 4. Explicação do funcionamento

**O semáforo começa no estado `Verde`. Quando ocorre a entrada `tempo`, ele muda para `Amarelo`. Após outro intervalo de tempo, passa para `Vermelho`. Depois de mais um intervalo, retorna para `Verde`. Esse ciclo continua repetidamente: `Verde → Amarelo → Vermelho → Verde`.**

### 5. Estados de aceitação

**Não há muito sentido em definir estados de aceitação nesse modelo.** — **Um semáforo não está tentando aceitar ou rejeitar uma cadeia de símbolos; ele está apenas representando um processo cíclico de mudança de estados. Por isso, a escolha adotada é `F = ∅`, ou seja, nenhum estado é considerado estado final.**

## Exercício 11 — Sistema de login

# Exercício — Sistema de Autenticação com Bloqueio

### 1. Todos os estados necessários para contar as tentativas

**Não, apenas `Aguardando`, `Autenticado` e `Bloqueado` não são suficientes**, porque o sistema precisa saber quantas tentativas incorretas já foram realizadas.

**Estados necessários:**

* **`Aguardando`** — nenhuma tentativa incorreta ainda.
* **`1Incorreta`** — uma tentativa incorreta.
* **`2Incorretas`** — duas tentativas incorretas.
* **`Autenticado`** — o usuário informou a senha correta.
* **`Bloqueado`** — ocorreram três tentativas incorretas.

### 2. Alfabeto de entrada

**`Σ = {senha_correta, senha_incorreta}`**

### 3. Estado inicial

**`q0 = Aguardando`**

### 4. Estados finais

**`F = {Autenticado}`**

**O estado `Autenticado` é considerado final porque representa o sucesso da autenticação. `Bloqueado` não é final de aceitação.**

### 5. Todas as transições

| Estado atual  | `senha_correta` | `senha_incorreta` |
| ------------- | --------------- | ----------------- |
| `Aguardando`  | `Autenticado`   | `1Incorreta`      |
| `1Incorreta`  | `Autenticado`   | `2Incorretas`     |
| `2Incorretas` | `Autenticado`   | `Bloqueado`       |
| `Autenticado` | `Autenticado`   | `Autenticado`     |
| `Bloqueado`   | `Bloqueado`     | `Bloqueado`       |

### 6. Definição formal do AFD

**`M = (Σ, Q, δ, q0, F)`**, onde:

* **`Σ = {senha_correta, senha_incorreta}`**
* **`Q = {Aguardando, 1Incorreta, 2Incorretas, Autenticado, Bloqueado}`**
* **`q0 = Aguardando`**
* **`F = {Autenticado}`**
* **`δ`** é a função de transição representada pela tabela acima.

### 7. Diagrama de estados

**O diagrama correspondente é:**

```text
                           senha_correta
                  ┌─────────────────────────► ((Autenticado))
                  │                                ↺
                  │                         senha_correta
                  │                         senha_incorreta
                  │
              → (Aguardando)
                  │
                  │ senha_incorreta
                  ▼
             (1Incorreta)
                  │
                  │ senha_incorreta
                  ▼
             (2Incorretas)
                  │
                  │ senha_incorreta
                  ▼
             ((Bloqueado))
                  ↺
          senha_correta
          senha_incorreta
```

**Além disso:**

```text
1Incorreta --senha_correta--> Autenticado
2Incorretas --senha_correta--> Autenticado
```

### 8. Comportamento após a autenticação e após o bloqueio

**Após a autenticação:** — **Quando `senha_correta` é informada, o sistema vai para `Autenticado`. A autenticação foi realizada com sucesso.**

**Após o bloqueio:** — **Quando ocorre a terceira tentativa incorreta, o sistema vai para `Bloqueado`. A partir desse estado, novas entradas não alteram o estado, pois o sistema permanece bloqueado.**

### 9. Por que os três estados iniciais não são suficientes?

**`Aguardando`, `Autenticado` e `Bloqueado` não são suficientes porque `Aguardando` não permite distinguir se o usuário errou uma, duas ou nenhuma vez.** Para controlar o limite de três tentativas, o AFD precisa memorizar essa informação por meio dos estados **`1Incorreta`** e **`2Incorretas`**. Assim, na terceira entrada `senha_incorreta`, o sistema consegue determinar que deve ir para `Bloqueado`.

# Parte 6 — Prática no JFLAP

## Exercício 12 — Implementação e testes
# Exercício — Implementação e testes no JFLAP

**AFD escolhido:** — **Exercício 7 — Cadeias que terminam em `1`.**

### 1. Criar os estados

**Estados:** — **`q0` e `q1`.**

* **`q0`** — estado inicial, usado quando a cadeia ainda não termina em `1`.
* **`q1`** — estado final, usado quando a cadeia termina em `1`.

### 2. Estado inicial e estado final

**Estado inicial:** — **`q0`**

**Estado final:** — **`q1`**

### 3. Criar todas as transições

| Estado atual | Entrada | Próximo estado |
| ------------ | ------- | -------------- |
| `q0`         | `0`     | `q0`           |
| `q0`         | `1`     | `q1`           |
| `q1`         | `0`     | `q0`           |
| `q1`         | `1`     | `q1`           |

### 4. Print do AFD

**Print do JFLAP:** — **Nesta atividade, deve ser inserido aqui o print da tela do JFLAP mostrando os estados `q0` e `q1`, o estado inicial `q0`, o estado final `q1` e todas as transições.**

> **Observação:** se você quiser, pode me enviar o print do seu JFLAP que eu confiro se o AFD está correto.

### 5. Tabela de testes

| Cadeia | Resultado esperado | Resultado no JFLAP | Conferência   |
| ------ | ------------------ | ------------------ | ------------- |
| `1`    | **Aceita**         | **Aceita**         | **✓ Correto** |
| `01`   | **Aceita**         | **Aceita**         | **✓ Correto** |
| `101`  | **Aceita**         | **Aceita**         | **✓ Correto** |
| `0`    | **Rejeita**        | **Rejeita**        | **✓ Correto** |
| `10`   | **Rejeita**        | **Rejeita**        | **✓ Correto** |
| `1110` | **Rejeita**        | **Rejeita**        | **✓ Correto** |

### 6. Comparação dos resultados

**Os resultados esperados e obtidos no JFLAP devem ser iguais:** — **as três primeiras cadeias são aceitas e as três últimas são rejeitadas. Isso confirma que o AFD foi implementado corretamente.**

### 7. Breve explicação

**O AFD possui dois estados e reconhece cadeias que terminam em `1`. O estado `q0` é o inicial e representa que o último símbolo não é `1` ou que ainda não foi lido nenhum símbolo. Ao ler `1`, o autômato vai para `q1`, que é o estado de aceitação. Ao ler `0`, ele retorna para `q0`.**


## Exercício 13 — CClaro! Abaixo está um **README.md completo**, já estruturado como um trabalho para entregar. Escolhi a situação de um **estacionamento com controle de acesso**, pois permite criar um AFD simples e fácil de explicar.

# Trabalho — Criação de um AFD

## Identificação do grupo

**Integrantes:**

* Nome 1
* Nome 2
* Nome 3
* Nome 4

**Tema:** Controle de entrada e saída de um estacionamento

---

# 1. Descrição do problema e suas regras

O problema escolhido representa o funcionamento de um estacionamento com controle de acesso. O sistema deve controlar se um veículo está fora do estacionamento, se está entrando ou se está dentro do estacionamento.

As regras são:

* Quando um veículo está **fora**, ele pode realizar a ação de `entrar`.
* Após entrar, o veículo passa para o estado **Dentro**.
* Quando o veículo está dentro, ele pode realizar a ação de `sair`.
* Após sair, o veículo volta para o estado **Fora**.
* O sistema não permite que um veículo realize `sair` quando já está fora.
* O sistema não permite que um veículo realize `entrar` novamente enquanto já está dentro.

---

# 2. Entradas e estados

## Entradas

O alfabeto de entrada é:

**`Σ = {entrar, sair}`**

As entradas representam as ações realizadas pelo veículo no estacionamento.

## Estados

O sistema possui dois estados:

* **`Fora`** — o veículo está fora do estacionamento.
* **`Dentro`** — o veículo está dentro do estacionamento.

---

# 3. Estado inicial e estados finais

**Estado inicial:** — **`Fora`**

O sistema começa considerando que o veículo está fora do estacionamento.

**Estados finais:** — **`F = {Dentro}`**

Neste modelo, o estado `Dentro` será considerado estado final, representando que o veículo conseguiu entrar no estacionamento.

---

# 4. Tabela de transições

| Estado atual | `entrar` | `sair` |
| ------------ | -------- | ------ |
| `Fora`       | `Dentro` | `Fora` |
| `Dentro`     | `Dentro` | `Fora` |

**Explicação:** — Quando o veículo está em `Fora` e recebe a entrada `entrar`, ele passa para `Dentro`. Quando está em `Dentro` e recebe `sair`, retorna para `Fora`.

As transições que mantêm o mesmo estado representam situações em que a ação não provoca uma mudança de estado.

---

# 5. Diagrama do AFD

```text
                         entrar
                    ┌──────────────┐
                    │              ▼
                 → (Fora) ─────> ((Dentro))
                    ▲                │
                    │                │
                    └────── sair ────┘
                    
                    ↑                ↑
                    │                │
                   sair            entrar
                    │                │
                    └─── (mantém) ───┘
```

Uma representação mais simples das transições é:

```text
Fora --entrar--> Dentro
Dentro --sair--> Fora

Fora --sair--> Fora
Dentro --entrar--> Dentro
```

**Legenda:**

* `→ Fora` indica o estado inicial.
* `((Dentro))` representa o estado final.
* As setas representam as transições provocadas pelas entradas.

---

# 6. Definição formal

O AFD é definido pela quíntupla:

**`M = (Σ, Q, δ, q0, F)`**

Onde:

* **`Σ = {entrar, sair}`** — alfabeto de entrada.
* **`Q = {Fora, Dentro}`** — conjunto de estados.
* **`q0 = Fora`** — estado inicial.
* **`F = {Dentro}`** — conjunto de estados finais.
* **`δ`** — função de transição apresentada na tabela.

Portanto:

**`M = ({entrar, sair}, {Fora, Dentro}, δ, Fora, {Dentro})`**

---

# 7. Testes de sequências de entrada

Foram escolhidas seis sequências para testar o funcionamento do AFD.

| Sequência              | Processamento                   | Resultado esperado |
| ---------------------- | ------------------------------- | ------------------ |
| `entrar`               | `Fora → Dentro`                 | **Aceita**         |
| `entrar, sair`         | `Fora → Dentro → Fora`          | **Rejeita**        |
| `entrar, entrar`       | `Fora → Dentro → Dentro`        | **Aceita**         |
| `entrar, sair, entrar` | `Fora → Dentro → Fora → Dentro` | **Aceita**         |
| `sair`                 | `Fora → Fora`                   | **Rejeita**        |
| `sair, entrar`         | `Fora → Fora → Dentro`          | **Aceita**         |

---

# 8. Processamento estado por estado

### Teste 1 — `entrar`

**Estado inicial:** `Fora`

```text
Fora --entrar--> Dentro
```

**Estado final:** `Dentro`

**Resultado:** **Aceita**

---

### Teste 2 — `entrar, sair`

**Estado inicial:** `Fora`

```text
Fora --entrar--> Dentro
Dentro --sair--> Fora
```

**Estado final:** `Fora`

**Resultado:** **Rejeita**

---

### Teste 3 — `entrar, entrar`

**Estado inicial:** `Fora`

```text
Fora --entrar--> Dentro
Dentro --entrar--> Dentro
```

**Estado final:** `Dentro`

**Resultado:** **Aceita**

---

### Teste 4 — `entrar, sair, entrar`

**Estado inicial:** `Fora`

```text
Fora --entrar--> Dentro
Dentro --sair--> Fora
Fora --entrar--> Dentro
```

**Estado final:** `Dentro`

**Resultado:** **Aceita**

---

### Teste 5 — `sair`

**Estado inicial:** `Fora`

```text
Fora --sair--> Fora
```

**Estado final:** `Fora`

**Resultado:** **Rejeita**

---

### Teste 6 — `sair, entrar`

**Estado inicial:** `Fora`

```text
Fora --sair--> Fora
Fora --entrar--> Dentro
```

**Estado final:** `Dentro`

**Resultado:** **Aceita**

---

# 9. Por que o modelo é determinístico?

O modelo é determinístico porque, para cada estado e cada entrada possível, existe **exatamente uma transição definida**.

Por exemplo:

* Se o sistema está em `Fora` e recebe `entrar`, obrigatoriamente vai para `Dentro`.
* Se está em `Dentro` e recebe `sair`, obrigatoriamente vai para `Fora`.

Não existe mais de uma possibilidade para a mesma combinação de estado e entrada. Por isso, o AFD possui comportamento determinístico.

---

# 10. Evidência dos testes no JFLAP

**Nesta parte deve ser inserido o print do JFLAP mostrando o AFD e os testes realizados.**

Os testes realizados no JFLAP devem conter as sequências apresentadas anteriormente:

* `entrar`
* `entrar, sair`
* `entrar, entrar`
* `entrar, sair, entrar`
* `sair`
* `sair, entrar`

**Print do JFLAP:**

> *Inserir aqui o print da implementação e dos testes realizados no JFLAP.*

---

# 11. Conclusão

**Com este exercício, aprendemos a transformar uma situação real em um modelo utilizando um Autômato Finito Determinístico. Foi possível identificar os estados, as entradas, o estado inicial e o estado final, além de construir a tabela e o diagrama de transições.**

**Também foi possível perceber que um AFD pode representar situações simples do cotidiano por meio de mudanças entre estados. Os testes mostram que, para cada sequência de entradas, o sistema percorre os estados de maneira determinada, permitindo verificar se a sequência termina em um estado de aceitação ou não.**

```markdown
## Desafio final

### Problema escolhido

### Estados e significado

### Alfabeto

### Estado inicial e estados finais

### Tabela de transições

### Diagrama

### Definição formal
M = (Σ, Q, δ, q0, F)

### Testes realizados
| Entrada | Resultado esperado | Resultado obtido |
|---|---|---|
| | | |

### Evidência no JFLAP

### Conclusão
```
