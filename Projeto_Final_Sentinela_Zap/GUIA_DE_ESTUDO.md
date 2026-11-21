# 📘 Guia de Estudo: Sentinela Zap

> Resumo para **estudar e explicar o projeto na prova**. Cada conceito das Aulas 12 e 13 aparece ligado a **onde ele está no nosso projeto**.
>
> **Como estudar:** leia primeiro a **Parte 0**, que explica o sistema do zero, como para uma criança. Depois passe para as seções 1 a 10, que trazem os nomes técnicos que a professora vai cobrar. A **seção 11** cobre a aula de ética (17/11) e a **seção 12** traz o que você preencheu na ficha de avaliação.
>
> **Prova A1: 24/11/2026.**

---

## 0. COMECE AQUI: o sistema explicado do zero

### 0.1 A ideia em uma história

Imagine que a Dona Marta tem um **porteiro muito esperto** no celular. Toda mensagem que chega passa por ele antes de ela responder. Ele faz três coisas, sempre na mesma ordem:

1. **Olha a mensagem com uma lupa 🔍** e anota as pistas. Tem link? Pede dinheiro? Pede código? Diz que trocou de número?
2. **Dá um palpite 🤔**: "acho que é **golpe**", "acho que é **spam**" ou "acho que é **legítima**". Ele aprendeu a palpitar vendo 150 mensagens de exemplo que já vinham com a resposta certa.
3. **Consulta o livro de regras 📖** e decide o que fazer: **LIBERAR**, **VERIFICAR**, **SILENCIAR**, **ARQUIVAR**, **NÃO PAGAR AINDA** ou **BLOQUEAR E DENUNCIAR**. Se faltar alguma informação, ele **pergunta** para a Dona Marta. No final, ele **explica** quais regras usou.

Cada parte da história tem um nome técnico:

| Na história | Nome técnico | Matéria |
|---|---|---|
| A lupa que acha pistas | **Extração de atributos** com **AFD** e **expressões regulares** | Linguagens Formais e Autômatos |
| O palpite aprendido com exemplos | **Árvore de decisão**, um modelo de **aprendizado supervisionado** | Aprendizado de máquina (Aula 13) |
| O livro de regras | **Base de conhecimento**: 18 regras SE…ENTÃO | Sistemas especialistas (Aula 12) |
| O porteiro lendo o livro e decidindo | **Motor de inferência** | Sistemas especialistas (Aula 12) |
| O caderninho onde ele anota tudo | **Memória de trabalho** | Sistemas especialistas (Aula 12) |
| "Eu decidi isso por causa daquilo" | **Mecanismo de explicação** ("Como?" e "Por quê?") | Sistemas especialistas (Aula 12) |

> **Frase para decorar:** "A lupa acha as pistas, a árvore dá o palpite e as regras dão a decisão."

### 0.2 Palavras que você precisa entender

- **Fato:** uma informação sobre a mensagem, anotada no caderninho. Exemplo: `pede_dinheiro = sim`.
- **Fato do tipo sim/não (booleano):** só pode ser **verdadeiro** (True) ou **falso** (False).
- **Regra:** uma frase do tipo **SE** (condições) **ENTÃO** (conclusão). Exemplo: "SE pede código ENTÃO é clonagem".
- **Disparar uma regra:** quando todas as condições do SE são verdadeiras, a regra "dispara" e o ENTÃO é anotado no caderninho.
- **Memória de trabalho:** o caderninho com todos os fatos do caso que está sendo analisado.
- **Prioridade:** quando várias regras podem disparar ao mesmo tempo, a de **número maior fala primeiro** (100, depois 95, depois 90, depois 50).
- **Refração:** cada regra dispara **no máximo uma vez** e só se trouxer **informação nova** para o caderninho.
- **Desconhecido:** é o que o sistema anota quando a pessoa responde **"não sei"**. "Não sei" é **diferente** de "não".

### 0.3 Etapa 1: a lupa (as pistas do texto)

Primeiro o texto é "limpo": tudo vira minúscula e os acentos são retirados ("Código" vira "codigo"). Depois a lupa procura **14 pistas**. O modelo usa as 14. As **4 mais importantes** viram fatos de sim/não para as regras:

| Fato para as regras | Pergunta que a lupa faz | Como ela descobre | Exemplo que dá **sim** |
|---|---|---|---|
| `tem_link` | Tem algum link? | Um **AFD** lê cada palavra, letra por letra, e aceita se começa com `http://`, `https://` ou `www.` | "acesse **https://banco-falso.site**" |
| `pede_dinheiro` | A pessoa está **pedindo** dinheiro? | Uma **expressão regular** procura um verbo de pedido (empresta, faz, manda, paga…) seguido, **na mesma frase**, de algo de dinheiro (pix, reais, R$, boleto…) | "**me faz um pix** de 700 reais" |
| `pede_codigo` | Pede código, senha ou token? | Expressão regular: "código de verificação", "manda o código", "token", "senha do banco"… | "**me passa o código** que chegou no SMS" |
| `troca_numero` | Diz que trocou de número? | Expressão regular: "troquei de número", "número novo", "celular quebrou", "perdi o chip"… | "Oi mãe, **troquei de número**" |

> ⚠️ **Detalhe importante:** "**já fiz o pix**" **não** é pedido de dinheiro, porque não tem verbo de pedido antes do "pix". Já "**me faz um pix**" é pedido. É por isso que o atributo se chama `pedido_dinheiro` e não apenas "fala de dinheiro".

As outras 10 pistas servem **só para o palpite do modelo**:

| Pista | O que conta |
|---|---|
| `qtd_links` | Quantos links existem (é o AFD contando) |
| `qtd_dinheiro` | Quantas palavras de dinheiro aparecem (pix, boleto, reais…), **mesmo sem ser pedido** |
| `qtd_urgencia` | Palavras de pressa: urgente, agora, hoje, bloqueado, expira… |
| `qtd_promocional` | Palavras de propaganda: promoção, desconto, grátis, sorteio, cupom… |
| `qtd_corrente` | Palavras de corrente: compartilhe, repasse, encaminhe… |
| `qtd_dados_sensiveis` | Pedido de dados: CPF, senha, login, cadastro, endereço… |
| `qtd_institucional` | Fingir ser empresa: prezado cliente, sua conta, central, FGTS… |
| `qtd_chamada_acao` | Mandar fazer algo: clique, acesse, cadastre, confira, aproveite… |
| `qtd_tom_pessoal` | Tom de conhecido: oi, mãe, pai, amiga, mano, saudade… |
| `pct_maiusculas` e `qtd_exclamacoes` | Quanto a mensagem "grita": letras MAIÚSCULAS e pontos de exclamação |

### 0.4 Etapa 2: o palpite (a árvore de decisão)

A árvore de decisão funciona como o jogo de **"adivinhe em perguntas"**. Ela aprendeu sozinha, com as **112 mensagens de treino**, quais perguntas separam melhor golpe, spam e legítima. Por exemplo: "tem pedido de dinheiro? Se sim, quantas palavras de urgência?". No fim das perguntas ela chega numa **folha**, que é o palpite.

- Ela foi testada com **38 mensagens que nunca tinha visto** e acertou **76,3%**.
- O palpite vira um fato no caderninho: **`previsao = golpe`**, **`spam`** ou **`legitima`**.
- **Esse é o ponto de integração** entre o aprendizado de máquina e o sistema especialista: o palpite do modelo entra como **fato** nas regras.

### 0.5 As 2 perguntas que o sistema pode fazer para você

Algumas informações **não estão no texto**. Só a pessoa sabe. Por isso o sistema pergunta, mas **só quando alguma regra precisa** da resposta:

| Fato | Pergunta | Respostas possíveis |
|---|---|---|
| `contato_salvo` | "O número que enviou a mensagem está salvo nos seus contatos?" | sim / não / não sei |
| `confirmou_por_ligacao` | "Você ligou para o número ANTIGO/conhecido da pessoa e ela confirmou o pedido?" | sim / não / não sei |

Junto com a pergunta, o sistema diz **"Por quê?"**: qual regra precisa daquela resposta. Exemplo: *"Pergunto porque a regra R07 precisa desse fato."*

### 0.6 Etapa 3: as 18 regras, uma por uma

As regras são divididas em **3 famílias**. Pense nelas assim:

| Família | Regras | Prioridade | O que faz | Comparação |
|---|---|---|---|---|
| 🕵️ **Detetives** | R01 a R05 | 100 (falam primeiro) | Só **anotam conclusões no caderninho**. **Não decidem** a ação. | O detetive junta pistas e escreve "isso é golpe do falso parente". |
| 🚨 **Alarmes de golpe conhecido** | R06 a R09 | 95 e 90 | **Decidem a ação** quando um golpe famoso foi reconhecido. **Vencem o modelo.** | O alarme de incêndio: se tocou, não importa o palpite de ninguém. |
| 🤝 **Usam o palpite do modelo** | R10 a R18 | 50 (falam por último) | Juntam o **palpite** + se o **contato é salvo** + se há **sinal de risco** e decidem. | O juiz que ouve o palpite e o contexto antes de decidir. |

#### 🕵️ Família 1: os detetives (anotam fatos novos)

**R01: golpe do falso parente**

- **Regra:** SE `troca_numero` = sim **E** `pede_dinheiro` = sim, ENTÃO anota `padrao_falso_parente` = sim.
- **Em palavras simples:** se alguém diz que mudou de número **e** já pede dinheiro, é o golpe mais famoso do WhatsApp.
- **Exemplo:** "Oi mãe, troquei de número. Me faz um pix de 700 reais agora?"
- **Por que existe:** é o golpe da Dona Marta. Não tem link nem erro de português, então só uma regra de especialista pega com certeza.
- **Repare:** precisa das **duas** condições. "Troquei de número, salva esse aqui" (sem pedir dinheiro) **não** dispara a R01. Esse é o **teste negativo**.

**R02: clonagem de conta**

- **Regra:** SE `pede_codigo` = sim, ENTÃO anota `padrao_clonagem` = sim.
- **Em palavras simples:** se pedem o código que chegou por SMS, a senha do banco ou um token, querem roubar sua conta.
- **Exemplo:** "Olá, sou do banco. Seu token expirou. Me informe o token."
- **Por que existe:** orientação oficial do WhatsApp: **nunca** compartilhe o código. Nenhuma empresa séria pede isso.

**R03: link é sinal de risco**

- **Regra:** SE `tem_link` = sim, ENTÃO anota `sinal_risco` = sim.
- **Em palavras simples:** link pode levar a site falso. Já é motivo para ficar atento.

**R04: pedido de dinheiro é sinal de risco**

- **Regra:** SE `pede_dinheiro` = sim, ENTÃO anota `sinal_risco` = sim.
- **Em palavras simples:** pedir dinheiro é o objetivo final de quase todo golpe.

**R05: sem link e sem pedido, não há sinal de risco**

- **Regra:** SE `tem_link` = não **E** `pede_dinheiro` = não, ENTÃO anota `sinal_risco` = não.
- **Em palavras simples:** se não tem link nem pedido de dinheiro, o golpista ainda não tem "por onde" roubar.

> 💡 **O que é "sinal de risco"?** É um fato **intermediário**, criado pelas regras R03, R04 e R05. Ele responde: "esta mensagem tem um caminho para o golpe acontecer (link ou pedido de dinheiro)?". Um fato criado por uma regra e usado por outra se chama **fato derivado**. Isso é **encadeamento**: uma regra prepara o terreno para a próxima.
>
> 💡 **E se tiver link E pedido de dinheiro?** A R03 dispara primeiro e anota `sinal_risco = sim`. A R04 **não dispara**, porque anotaria a mesma coisa e não traria informação nova. Isso é a **refração**.

#### 🚨 Família 2: alarmes de golpe conhecido (decidem e vencem o modelo)

**R06: clonagem → BLOQUEAR E DENUNCIAR** (prioridade 95)

- **Regra:** SE `padrao_clonagem` = sim, ENTÃO **BLOQUEAR E DENUNCIAR**.
- **O que o sistema recomenda:** nunca envie códigos, senhas ou tokens. Bloqueie, denuncie e ative a confirmação em duas etapas.
- **Por que tem prioridade 95, a maior entre as que decidem:** passar o código entrega a conta **na hora**. Não existe situação em que isso seja seguro.
- **Exemplo famoso do projeto:** o modelo achou que *"Me informe o token…"* era **legítima**. A R02 e a R06 bloquearam mesmo assim. É o **especialista corrigindo o modelo**.

**R07: falso parente sem confirmação → BLOQUEAR E DENUNCIAR** (prioridade 90)

- **Regra:** SE `padrao_falso_parente` = sim **E** `confirmou_por_ligacao` = **não**, ENTÃO **BLOQUEAR E DENUNCIAR**.
- **Em palavras simples:** você ligou para o número antigo e a pessoa **não** confirmou, ou você não ligou. Então é golpe.
- **O que o sistema recomenda:** não transfira, ligue para o número ANTIGO e, se não for a pessoa, bloqueie, denuncie e avise a família.
- **É o teste positivo** do trabalho.

**R08: falso parente confirmado → LIBERAR COM CAUTELA** (prioridade 90)

- **Regra:** SE `padrao_falso_parente` = sim **E** `confirmou_por_ligacao` = **sim**, ENTÃO **LIBERAR COM CAUTELA**.
- **Em palavras simples:** você ligou e era mesmo seu filho. Pode ajudar, mas confira o **nome do dono da chave Pix** antes de pagar.

**R09: falso parente, confirmação desconhecida → NÃO PAGAR AINDA** (prioridade 90)

- **Regra:** SE `padrao_falso_parente` = sim **E** `confirmou_por_ligacao` = **desconhecido**, ENTÃO **NÃO PAGAR AINDA**.
- **Em palavras simples:** você respondeu "não sei". O sistema não manda bloquear (não tem certeza que é golpe) nem liberar (não tem certeza que é seguro). Ele manda **esperar e ligar primeiro**.
- **É o teste de informação ausente.** Frase da Aula 12: **"não saber" é diferente de "saber que não"**.

> 💡 **Por que essas regras vencem o modelo?** Elas têm prioridade **90 ou 95**, maior que a das regras do modelo (**50**). Se o modelo errar o palpite, o golpe conhecido continua sendo pego.

#### 🤝 Família 3: regras que usam o palpite do modelo (prioridade 50)

Estas regras só agem quando **nenhum alarme de golpe conhecido** tocou. Cada uma olha 3 coisas: o **palpite** do modelo, se o **contato está salvo** e se há **sinal de risco**. Dá para decorar tudo nesta tabela:

| Palpite do modelo | Contato salvo? | Mais alguma condição? | Regra | AÇÃO |
|---|---|---|---|---|
| **golpe** | não | sinal de risco = **sim** | **R10** | 🔴 BLOQUEAR E DENUNCIAR |
| **golpe** | não | sinal de risco = **não** | **R11** | 🟡 VERIFICAR |
| **golpe** | **sim** | – | **R12** | 🟡 VERIFICAR (conta pode estar clonada) |
| **spam** | não | – | **R13** | 🟠 SILENCIAR E DENUNCIAR SPAM |
| **spam** | **sim** | – | **R14** | ⚪ ARQUIVAR |
| **legítima** | **sim** | pede dinheiro = **não** | **R15** | 🟢 LIBERAR |
| **legítima** | **sim** | pede dinheiro = **sim** | **R16** | 🟡 VERIFICAR (conta pode estar clonada) |
| **legítima** | não | sinal de risco = **sim** | **R17** | 🟡 VERIFICAR |
| **legítima** | não | sinal de risco = **não** | **R18** | 🟢 LIBERAR |

Agora uma por uma:

**R10: golpe + número desconhecido + sinal de risco → BLOQUEAR E DENUNCIAR**

- O modelo acha que é golpe, você não conhece o número e tem link ou pedido de dinheiro. Tudo aponta para golpe.
- **Exemplo real do notebook (Caso 1):** "Prezado cliente, seu cartão foi bloqueado hoje. Desbloqueie agora: https://cartao-desbloqueio.site", vindo de número desconhecido.

**R11: golpe + número desconhecido, sem sinal de risco → VERIFICAR**

- O modelo desconfia, mas ainda não tem link nem pedido de dinheiro. Pode ser o golpista "puxando conversa" antes de pedir. Ou pode ser alguém de verdade com número novo.
- **Por que não bloquear:** sem link e sem pedido não tem como roubar ainda. Bloquear poderia atingir uma pessoa inocente. Esta regra foi criada depois que o sistema **bloqueava por engano** a mensagem legítima *"Mãe, vou chegar mais tarde hoje"*.

**R12: golpe + contato salvo → VERIFICAR**

- A mensagem parece golpe, mas veio de alguém que você conhece. A **conta dessa pessoa pode ter sido clonada**. Ligue para ela antes de qualquer coisa.
- **Exemplo real do notebook (Caso 3):** "Amiga, tô sem acesso ao app do banco, me empresta 300 no pix?", vindo de contato salvo.

**R13: spam + número desconhecido → SILENCIAR E DENUNCIAR SPAM**

- Propaganda que você não pediu, de quem você não conhece. Denuncie como spam.

**R14: spam + contato salvo → ARQUIVAR**

- Propaganda ou corrente de um conhecido, como o tio que manda "bom dia" com corrente. Não é perigoso, só chato. Arquive e não repasse.
- **Exemplo real do notebook (Caso 2):** "Bom dia!!! Compartilhe essa mensagem com 10 amigos e receba boas notícias amanhã!", vindo de contato salvo.

**R15: legítima + contato salvo + sem pedido de dinheiro → LIBERAR**

- Mensagem normal de quem você conhece e sem pedir dinheiro. Pode ler e responder tranquilo.

**R16: legítima + contato salvo + pedido de dinheiro → VERIFICAR**

- O texto parece normal e vem de um conhecido, **mas pede dinheiro**. Contas clonadas usam justamente o nome de conhecidos. Ligue antes de pagar.
- **Por que existe:** é uma **rede de segurança** para quando o modelo errar e chamar um pedido de dinheiro de "legítima". No dataset atual o modelo já chama esses pedidos de golpe (e quem decide é a R12), por isso a R16 não dispara nos testes. A **verificação das 432 combinações** prova que ela pode disparar (é **alcançável**).

**R17: legítima + número desconhecido + sinal de risco → VERIFICAR**

- Parece normal, mas vem de número desconhecido e tem link ou pedido de dinheiro. Confirme quem é antes de clicar ou pagar.

**R18: legítima + número desconhecido, sem sinal de risco → LIBERAR**

- Número desconhecido, mas sem link e sem pedido. Exemplo: "Oi mãe, troquei de número, salva esse aqui. Beijos!". Pode responder, é só ter cuidado se depois vier um pedido.

#### E se nenhuma regra conseguir decidir?

O resultado é **INCONCLUSIVO**: "não clique, não pague e peça ajuda a alguém de confiança". O motor também para se passar de **50 ciclos**, para nunca ficar rodando para sempre.

### 0.7 O caminho que o sistema faz (para desenhar na prova)

Por causa das prioridades, a decisão sempre segue esta ordem:

```
1. Pede código, senha ou token?
      SIM → R02 + R06 → BLOQUEAR E DENUNCIAR (fim)
      NÃO → passo 2

2. Diz que trocou de número E pede dinheiro? (R01)
      SIM → pergunta: "você ligou e a pessoa confirmou?"
              não     → R07 → BLOQUEAR E DENUNCIAR
              sim     → R08 → LIBERAR COM CAUTELA
              não sei → R09 → NÃO PAGAR AINDA
      NÃO → passo 3

3. Olha o palpite do modelo, pergunta se o contato está salvo
   e consulta a tabela da família 3 (R10 a R18).
```

> As regras R03, R04 e R05 rodam logo no começo e preparam o fato `sinal_risco`, que a família 3 usa.

### 0.8 Passo a passo real: a mensagem da Dona Marta

Isto é **exatamente** o que o notebook imprime no teste positivo (seção 12). Veja o que cada parte significa:

**Mensagem:** *"Oi mãe, troquei de número. Me faz um pix de 700 reais agora? É urgente, depois te explico."*

**Passo 1, a lupa:** o sistema anota os fatos do texto.
```
tem_link = False      (nenhuma palavra começa com http://, https:// ou www.)
pede_dinheiro = True  ("Me faz um pix de 700 reais")
pede_codigo = False
troca_numero = True   ("troquei de número")
```

**Passo 2, o palpite:** `PREVISÃO DO MODELO: golpe`. A folha da árvore tinha 100% de exemplos de golpe. Isso **não** é uma probabilidade garantida, é só a proporção dos exemplos de treino naquela folha.

**Passo 3, o motor lê as regras, ciclo por ciclo:**
```
Ciclo 1: R01 disparou → troca de número + pedido de dinheiro = padrão do falso parente
Ciclo 2: R04 disparou → pede dinheiro = há sinal de risco
Ciclo 3: PERGUNTA 'confirmou_por_ligacao' (porque a R07 precisa desse fato) → resposta: não
Ciclo 4: R07 disparou → falso parente + não confirmou = BLOQUEAR E DENUNCIAR
```

- **Ciclo 1:** as regras de prioridade 100 falam primeiro. A R01 tem as duas condições verdadeiras, então dispara.
- **Ciclo 2:** a R04 também é de prioridade 100 e traz informação nova (`sinal_risco`), então dispara. A R05 não dispara, porque exige "não pede dinheiro".
- **Ciclo 3:** nenhuma regra consegue disparar com o que está no caderninho. O motor procura a regra de **maior prioridade** que só depende de algo perguntável: é a **R07** (prioridade 90), que precisa saber se você ligou. Por isso ele pergunta sobre a **ligação**, e não sobre o contato salvo.
- **Ciclo 4:** com a resposta "não", a R07 dispara e define a ação. O objetivo foi alcançado, então o motor **para**.

**Passo 4, a explicação "COMO?"** (o sistema monta de trás para frente):
```
acao = BLOQUEAR E DENUNCIAR       [veio da R07]
   padrao_falso_parente = True    [veio da R01]
      troca_numero = True         [veio do texto]
      pede_dinheiro = True        [veio do texto]
   confirmou_por_ligacao = False  [veio do usuário]
```

**E se a resposta fosse diferente?** Esse é o exercício "mudando um só fato":

- Respondeu **"sim"** → R08 → **LIBERAR COM CAUTELA**.
- Respondeu **"não sei"** → R09 → **NÃO PAGAR AINDA** (teste de informação ausente).

### 0.9 Os 3 testes obrigatórios, em palavras simples

| Teste | Pergunta que ele responde | Mensagem | O que acontece |
|---|---|---|---|
| ✅ **Positivo** | "Quando tudo está presente, a regra dispara?" | "Oi mãe, troquei de número. Me faz um pix…" + **não** liguei | R01 e R07 disparam → **BLOQUEAR E DENUNCIAR** |
| ✅ **Negativo** | "Quando falta uma condição, a regra fica quieta?" | "Oi mãe, troquei de número, salva esse aqui. Beijos!" (sem pedir dinheiro, número desconhecido) | A R01 **não** dispara. O modelo diz legítima e a R18 manda **LIBERAR** |
| ✅ **Informação ausente** | "Quando a pessoa não sabe, o sistema trata diferente de 'não'?" | A mesma do falso parente + resposta **"não sei"** | A R09 dispara → **NÃO PAGAR AINDA** (e não BLOQUEAR) |

### 0.10 Como ler a saída do notebook

Toda análise impressa no notebook tem as mesmas partes:

| O que aparece | O que significa |
|---|---|
| `MENSAGEM:` | O texto analisado |
| `PREVISÃO DO MODELO:` | O palpite da árvore (Etapa 2) e a proporção na folha |
| `FATOS DO TEXTO:` | As 4 pistas de sim/não que a lupa achou (Etapa 1) |
| `PERGUNTOU:` + `Por quê?` | A pergunta feita ao usuário e qual regra precisava dela |
| `RASTRO DE INFERÊNCIA:` | Cada ciclo do motor: qual regra disparou ou o que foi perguntado |
| `>>> AÇÃO:` e `>>> RECOMENDAÇÃO:` | A decisão final e o conselho para a pessoa |
| `COMO o sistema chegou…` | A explicação de trás para frente, com a origem de cada fato (texto, regra ou usuário) |
| `AVISO:` | Lembrete de que a decisão final é humana |

---

## 1. O projeto em 30 segundos (para abrir qualquer resposta)

> "O **Sentinela Zap** é um **sistema especialista híbrido** que ajuda a pessoa a decidir o que fazer com uma mensagem suspeita de WhatsApp. Primeiro, **um autômato finito e expressões regulares** extraem atributos do texto. Depois, uma **árvore de decisão** (aprendizado supervisionado) prevê se a mensagem é **legítima, spam ou golpe**. Essa **previsão vira um fato** na memória de trabalho. Por fim, um **motor de inferência com 18 regras SE…ENTÃO** combina esse fato com outros fatos do texto e do usuário e decide a **ação**: liberar, verificar, silenciar ou bloquear. O sistema **explica** quais regras usou."

**A dor:** 2,26 milhões de estelionatos em 2025. 84% das tentativas de golpe chegam por apps de mensagem. A persona é a **Dona Marta, 63 anos**, que recebe o golpe do **falso parente** ("Oi mãe, troquei de número, me faz um Pix").

---

## 2. Arquitetura (desenhe isto na prova)

```
Mensagem
   │
   ▼
[1] EXTRAÇÃO DE ATRIBUTOS  ← LFA: AFD (links) + Expressões Regulares
   │                 │
   │ 14 atributos    │ fatos booleanos: tem_link, pede_dinheiro,
   ▼                 │                  pede_codigo, troca_numero
[2] ÁRVORE DE DECISÃO│  ← Aprendizado SUPERVISIONADO (aprende com dados)
   │ previsao = golpe/spam/legitima
   ▼                 ▼
[3] SISTEMA ESPECIALISTA  ← Regras escritas por humanos
    Memória de trabalho + Base de conhecimento (R01–R18) + Motor de inferência
    Pergunta ao usuário: contato_salvo? confirmou_por_ligacao?
   │
   ▼
AÇÃO + RECOMENDAÇÃO + EXPLICAÇÃO ("como?" e "por quê?")
```

**Frase-chave:** *"A parte que **aprende com dados** é a árvore de decisão. A parte que **aplica regras humanas** é o motor de inferência. A **integração** acontece quando a previsão da árvore entra como **fato** nas regras."*

---

## 3. Sistemas Especialistas (Aula 12)

| Conceito | Definição curta | No nosso projeto |
|---|---|---|
| **Sistema especialista** | Usa conhecimento de um **domínio restrito** para resolver problemas | Domínio: mensagens de texto do WhatsApp em português |
| **Base de conhecimento** | Conceitos e **regras** do domínio | Lista `REGRAS` com R01–R18 |
| **Memória de trabalho** | **Fatos do caso** e conclusões intermediárias | Dicionário `memoria`: `previsao`, `tem_link`, `sinal_risco`… |
| **Motor de inferência** | Escolhe e executa as regras aplicáveis | Classe `MotorInferencia` |
| **Interface** | Coleta respostas e mostra recomendações | Perguntas ao usuário e função `mostrar()` |
| **Mecanismo de explicação** | Registra a justificativa | Rastro de inferência + `explicar_como()` |
| **Fato** | Informação sobre o caso | `troca_numero = True` |
| **Regra** | SE condições ENTÃO conclusão | R01: SE troca_numero E pede_dinheiro ENTÃO padrão falso parente |
| **Especialista do domínio** | Fornece e valida o conhecimento | Orientações de WhatsApp, Febraban e Serasa |
| **Engenheiro do conhecimento** | Formaliza a base | Nós (a equipe) |

### Ciclo de inferência (decore os 3 passos)

1. **Identificar** as regras cujas condições estão satisfeitas.
2. **Escolher** uma regra (resolução de conflito) e **executar** a conclusão.
3. **Atualizar** os fatos e **repetir** até uma condição de parada.

### Encadeamento para FRENTE × para TRÁS
| | Para frente (*forward*) | Para trás (*backward*) |
|---|---|---|
| Parte de… | **Fatos** | Um **objetivo** ou hipótese |
| Pergunta | "O que os fatos permitem concluir?" | "Que evidências sustentam esta hipótese?" |
| No projeto | **É o que o motor usa** | Usado para **explicar** e para decidir o que **perguntar** |

**Exemplo para frente (nosso):** fatos `troca_numero` + `pede_dinheiro` → **R01** gera `padrao_falso_parente` → o usuário responde "não confirmei" → **R07** gera **BLOQUEAR E DENUNCIAR**.

**Exemplo para trás (nosso):** objetivo "BLOQUEAR E DENUNCIAR". A R07 exige `padrao_falso_parente` e `confirmou_por_ligacao = não`. O `padrao_falso_parente` vem da R01, que exige `troca_numero` e `pede_dinheiro`. O sistema **verifica** esses fatos no texto e **pergunta** a confirmação ao usuário.

> Nosso motor também tem um pouco de "para trás": ele só pergunta ao usuário quando **uma regra precisa** daquele fato. Isso é a resposta do **"Por quê?"**.

### Resolução de conflitos (quando duas regras servem)

- Usamos **prioridade** (100 → 95 → 90 → 50) e, em empate, a **ordem da base**.
- Regras de **padrão de golpe conhecido** (R06–R09) têm prioridade **maior** que as regras do modelo (R10–R18). Assim, **o especialista prevalece se o modelo errar**.
- **Refração:** cada regra dispara uma vez, e só se gerar **informação nova**.

### Condições de parada

1. O **objetivo** (`acao`) foi alcançado.
2. **Nenhuma regra** produz informação nova → `INCONCLUSIVO` e encaminhamento humano.
3. **Limite de ciclos** (50) para evitar laço infinito.

### Informação ausente (pergunta clássica!)
> *"Não saber se há papel é diferente de saber que não há papel."* (Aula 12)

No projeto: responder **"não sei"** à pergunta "você confirmou por ligação?" gera `confirmou_por_ligacao = desconhecido`, e a **R09** recomenda **NÃO PAGAR AINDA**. Responder **"não"** dispara a **R07** e leva a **BLOQUEAR**. São respostas diferentes, porque **ausência de evidência não é negação**.

### Explicação "Como?" e "Por quê?"

- **Como?** Mostra a cadeia: `acao` ← R07 ← (`padrao_falso_parente` ← R01 ← `troca_numero`, `pede_dinheiro`) + `confirmou_por_ligacao` (usuário).
- **Por quê?** Quando pergunta algo: "Pergunto porque a regra R07 precisa desse fato".

### Verificação × Validação
| | Pergunta | No projeto |
|---|---|---|
| **Verificação** | A base está bem construída? Há contradições, ciclos ou regras inalcançáveis? | Seção 16: testamos as **432 combinações** de fatos. Resultado: nenhuma contradição, nenhum ciclo, todas as regras alcançáveis |
| **Validação** | A recomendação atende aos casos reais? | Seções 8, 12, 13 e 15: acurácia, testes e casos novos |

### Casos de teste obrigatórios
| Teste | O que é | Nosso teste |
|---|---|---|
| **Positivo** | Todas as condições presentes, a regra dispara | Falso parente + "não confirmei" → R01 e R07 → BLOQUEAR |
| **Negativo** | Uma condição é falsa, a regra **não** dispara | "Troquei de número, salva esse" **sem** pedir dinheiro → R01 **não** dispara |
| **Informação ausente** | Falta um dado para decidir | Usuário não sabe se confirmou → R09 → NÃO PAGAR AINDA |

### Limitações de sistemas especialistas
Conhecimento tácito é difícil de formalizar, regras ficam desatualizadas e, fora do domínio, a resposta falha. No projeto: golpistas mudam a escrita ("p1x") e surgem golpes novos.

---

## 4. Aprendizado de Máquina (Aula 13)

| Conceito | Definição | No projeto |
|---|---|---|
| **Aprendizado de máquina** | O computador **aprende padrões com dados** em vez de receber regras prontas | A árvore aprende com 150 mensagens |
| **Supervisionado** | Treina com exemplos **com resposta conhecida** (rótulo) | Cada mensagem tem rótulo: legitima, spam ou golpe |
| **Atributos (X)** | Entradas que descrevem o caso | 14 atributos: `qtd_links`, `pedido_dinheiro`, `pede_codigo`… |
| **Rótulo (y)** | A resposta conhecida | `rotulo` |
| **Classificação** | Prevê uma **categoria** | ✅ Nosso caso: 3 classes |
| **Regressão** | Prevê um **número contínuo** | Exemplo: estimar o prejuízo em R$ |
| **Treino / teste** | Treino ensina; teste avalia em dados **não vistos** | 75% treino (112) e 25% teste (38), estratificado |
| **Overfitting** | Decorar o treino e ir mal em dados novos | Evitado limitando a profundidade e usando validação |
| **Acurácia** | % de acertos | 76,3% no teste |
| **Matriz de confusão** | Linhas = real; colunas = previsto | Mostra que golpe é confundido com spam |
| **Precisão** | Dos que previ como X, quantos eram X? | golpe: 0,78 |
| **Recall** | Dos que eram X, quantos acertei? | golpe: 0,58 (por isso as regras ajudam!) |
| **Validação cruzada** | Divide em k partes e testa k vezes | 5 partes: média de 81,3% |
| **GridSearchCV** | Testa combinações de hiperparâmetros com validação **só no treino** | Escolheu `max_depth=5`, `gini` |
| **Hiperparâmetro** | Configuração escolhida **antes** do treino | Profundidade máxima, critério, mínimo por folha |

### Árvore de decisão

- Aprende perguntas do tipo **"atributo ≤ valor?"** que separam as classes.
- É **interpretável**: dá para ler as condições (`export_text`).
- As condições da árvore **vêm dos dados**. As regras do sistema especialista **vêm de humanos**.
- `fit()` aprende com o treino. `predict()` prevê casos novos.

### Por que separar treino e teste?
Para medir se o modelo **generaliza**. Testar com os dados de treino mede só memorização.

### Por que não ajustar o modelo olhando o teste?
Porque o teste deixa de ser "dado novo" e a nota fica otimista. Usamos o **GridSearchCV** (validação dentro do treino), como a professora alertou.

### Por que 100% num teste pequeno não basta?
Poucos exemplos, ainda mais simulados, não representam a variedade real. O resultado é **instável**.

---

## 5. Linguagens Formais e Autômatos (o diferencial do projeto)

| Conceito | Definição | No projeto |
|---|---|---|
| **Alfabeto (Σ)** | Conjunto finito de símbolos | Os caracteres que não são espaço |
| **Palavra / cadeia** | Sequência finita de símbolos | Cada *token* da mensagem |
| **Linguagem** | Conjunto de palavras | L_URL = conjunto de todos os links |
| **Linguagem regular** | Reconhecida por um AFD ou descrita por uma ER | L_URL e todos os nossos padrões |
| **AFD** | M = (Q, Σ, δ, q₀, F) | `afd_url` |
| **Expressão regular** | Notação para linguagem regular | `^(https?://\|www\.)\S+$` |
| **Equivalência AFD ↔ ER** | Teorema de Kleene: definem a mesma classe de linguagens | Seção 7.3 testa 600+ tokens: **0 divergências** |
| **Estado morto** | Estado sem volta que rejeita | `q_morto` |

### O AFD de links
L_URL = {`http://`, `https://`, `www.`} · Σ⁺

- **Q** = {q0, h1, h2, h3, h4, h5, c1, c2, c3, w1, w2, w3, qf, q_morto}
- **q₀** = q0, **F** = {qf}
- Caminhos: q0 -h→ h1 -t→ h2 -t→ h3 -p→ h4 (-s→ h5) -:→ c1 -/→ c2 -/→ c3 -Σ→ qf ⟲Σ
- E também: q0 -w→ w1 -w→ w2 -w→ w3 -.→ c3
- Qualquer outro símbolo leva a q_morto.

**Exemplo de execução:** `www.site.com` percorre q0 → w1 → w2 → w3 → c3 → qf → qf… → **ACEITA**.
`http:/erro` percorre q0 → h1 → h2 → h3 → h4 → c1 → c2 → q_morto → **REJEITA**.

> **Por que é determinístico?** Para cada estado e símbolo existe **exatamente uma** transição. As transições ausentes vão para `q_morto`.

### Expressões regulares do projeto (exemplos)

- **Troca de número:** `(troquei|mudei) (de )?(numero|celular|chip)|numero novo|celular (quebrou|caiu)…`
- **Pedido de código:** `codigo de verificacao|(mand|pass|envi)\w* o codigo|token|senha do banco`
- **Pedido de dinheiro:** verbo de pedido (`empresta`, `faz`, `pague`…) seguido, na mesma frase, de algo monetário (`pix`, `boleto`, `R$`…). Por isso **"já fiz o pix"** **não** é um pedido.

---

## 6. As 18 regras (resumo)

> A explicação de cada regra, uma por uma e com exemplos, está na **Parte 0.6**. Aqui fica só o resumo para revisar.

| Grupo | Regras | O que fazem |
|---|---|---|
| **Intermediárias** (prioridade 100) | R01 falso parente · R02 clonagem · R03, R04 e R05 sinal de risco | Geram **fatos novos** (encadeamento) |
| **Padrões de golpe** (95/90) | R06 clonagem → BLOQUEAR · R07 falso parente sem confirmação → BLOQUEAR · R08 confirmado → LIBERAR COM CAUTELA · R09 confirmação desconhecida → NÃO PAGAR AINDA | **Conhecimento do especialista**, que prevalece sobre o modelo |
| **Usam a previsão** (50) | R10 golpe + desconhecido + risco → BLOQUEAR · R11 golpe + desconhecido sem risco → VERIFICAR · R12 golpe + contato salvo → VERIFICAR (conta clonada) · R13 spam + desconhecido → SILENCIAR · R14 spam + contato → ARQUIVAR · R15 legítima + contato sem pedido → LIBERAR · R16 legítima + contato com pedido → VERIFICAR · R17 legítima + desconhecido + risco → VERIFICAR · R18 legítima + desconhecido sem risco → LIBERAR | **Integração ML + SE** |

**Origem das regras:** [A] WhatsApp ("nunca compartilhe o código") · [B] Febraban e Serasa ("confirme por ligação") · [C] critério da equipe.

---

## 7. Números para decorar

| | |
|---|---|
| Mensagens no dataset | **150** (50 legítima, 50 spam, 50 golpe), **simuladas** |
| Treino / teste | **112 / 38** (75% / 25%, estratificado, `random_state=42`) |
| Atributos | **14** |
| Regras | **18** |
| Acurácia no teste | **76,3%** |
| Validação cruzada | **81,3%** ± 4,0% |
| Golpes com alerta: modelo / híbrido | **7/12 → 8/12** |
| Golpes liberados | **0** |
| Legítimas bloqueadas | **0** |
| Combinações verificadas | **432** |
| Erros reais usados na análise ética | **4 de 12** golpes de investimento viram spam · **1** mensagem de aula com link vira spam |
| Trecho do vídeo (ética) | **06:07 a 06:58**, Steven Gouveia, IEA-USP |
| Dor | 2,26 mi estelionatos (2025) · 84% via apps de mensagem · 26,3 mi vítimas |

---

## 8. Perguntas prováveis da prova (com respostas curtas)

**1. O que é um sistema especialista?**
Um sistema que usa o conhecimento de um domínio restrito, representado por fatos e regras, para resolver problemas como um especialista. Tem base de conhecimento, memória de trabalho e motor de inferência.

**2. Quais são os componentes do seu sistema?**
Base de conhecimento (R01–R18), memória de trabalho (o dicionário de fatos), motor de inferência (`MotorInferencia`), interface (perguntas e saída) e mecanismo de explicação (rastro e "como?").

**3. Qual parte aprende com os dados e qual aplica regras humanas?**
A árvore de decisão aprende com os dados. O motor de inferência aplica as regras escritas por nós.

**4. Como o aprendizado de máquina foi integrado ao sistema especialista?**
A previsão da árvore (`legitima`, `spam` ou `golpe`) é colocada na memória de trabalho como o fato `previsao`. As regras R10–R18 usam esse fato nas condições.

**5. Por que não usar só o classificador?**
Porque ele erra (recall de golpe de 0,58) e não usa contexto. As regras pegam padrões conhecidos que o modelo não aprendeu. O exemplo é o pedido de token, que o modelo chamou de legítima e a R02 bloqueou. Além disso, as regras usam fatos que não estão no texto, como o contato salvo, e **explicam** a decisão.

**6. Por que `contato_salvo` não é atributo do modelo?**
Porque não está no texto: é contexto que só o usuário sabe. Fora do modelo, mudar só esse fato mostra o efeito das regras. Exemplo: a mesma mensagem dá ARQUIVAR (contato salvo) ou SILENCIAR (desconhecido).

**7. Que tipo de encadeamento você usou? Por quê?**
Para frente, porque a tarefa é descobrir **o que decorre dos fatos** da mensagem. O motor só pergunta algo quando uma regra precisa daquele fato, e isso lembra o encadeamento para trás.

**8. O que acontece quando duas regras podem disparar?**
Resolução de conflito por **prioridade** e, em empate, pela ordem. Os padrões de golpe (90/95) vencem as regras do modelo (50).

**9. Como o sistema trata informação ausente?**
Ele pergunta ao usuário. Se a resposta for "não sei", o fato vira `desconhecido`, o que é diferente de "não", e a R09 recomenda NÃO PAGAR AINDA. Se nenhuma regra concluir, o resultado é INCONCLUSIVO, com encaminhamento humano.

**10. Quais são as condições de parada?**
O objetivo alcançado (`acao`), nenhuma regra gerando informação nova ou o limite de 50 ciclos.

**11. Explique o teste positivo, o negativo e o de informação ausente.**
Veja a tabela da seção 3. Positivo: R01 e R07 disparam. Negativo: sem pedido de dinheiro, a R01 não dispara. Ausente: "não sei" leva à R09.

**12. O que é aprendizado supervisionado?**
É treinar com exemplos rotulados (entrada e resposta conhecida) para prever o rótulo de casos novos.

**13. Diferença entre classificação e regressão?**
Classificação prevê uma categoria (o nosso caso). Regressão prevê um valor numérico.

**14. Por que separar treino e teste? Por que usar validação cruzada?**
Para avaliar em dados não vistos. A validação cruzada dá um resultado mais estável num dataset pequeno.

**15. O que a matriz de confusão mostrou?**
Que o principal erro é **golpe confundido com spam**, porque os dois têm promoção e exclamação. As regras compensam parte desse erro.

**16. Onde entra Linguagens Formais e Autômatos?**
Na extração de atributos. Um **AFD** reconhece links (L = {http://, https://, www.}·Σ⁺) e **expressões regulares** reconhecem os padrões de golpe. Também provamos a **equivalência AFD ↔ ER** nos tokens do dataset.

**17. Dê a definição formal do seu AFD.**
M = (Q, Σ, δ, q₀, F), com Q = {q0, h1–h5, c1–c3, w1–w3, qf, q_morto}, Σ = caracteres sem espaço, q₀ = q0, F = {qf} e δ conforme o diagrama (seção 5).

**18. Seu sistema é confiável para uso real?**
Não, sem mais validação. O dataset é simulado e pequeno, e as ERs têm vocabulário fixo. Seria preciso usar dados reais e fazer revisão com especialistas. O sistema sempre avisa que a decisão final é humana.

**19. Que evidência mostraria que a base precisa de revisão?**
Golpes novos sendo liberados, aumento de falsos positivos ou regras que nunca disparam (verificamos isso na seção 16).

**20. Qual conhecimento foi difícil transformar em regra?**
O "tom" de engenharia social, como pressão emocional e intimidade falsa. Usamos aproximações, como palavras de urgência e o pedido de dinheiro na mesma frase.

**21. O que é a R16 e por que ela "nunca dispara" no dataset?**
É uma **rede de segurança**: legítima + contato salvo + pedido de dinheiro → VERIFICAR. Ela não dispara porque o modelo atual já classifica pedidos de dinheiro como golpe. Se o modelo mudar ou errar, ela protege. A verificação exaustiva prova que ela é **alcançável**.

**22. Por que a R10 exige "sinal de risco"?**
Porque todo golpe precisa de um vetor: link, pedido de dinheiro ou código. Se o modelo desconfia mas não há vetor, a R11 manda **verificar** em vez de bloquear. Isso evita bloquear uma mensagem legítima de um número novo.

**23. O que é o fato `sinal_risco` e de onde ele vem?**
É um **fato derivado**, que não vem do texto nem do usuário: é criado por regras. A R03 (tem link) e a R04 (pede dinheiro) anotam `sinal_risco = sim`. A R05 (sem link e sem pedido) anota `sinal_risco = não`. Depois as regras R10, R11, R17 e R18 usam esse fato. Isso é **encadeamento**.

**24. Se a mensagem tem link E pede dinheiro, a R03 e a R04 disparam?**
Só a R03 dispara, porque vem primeiro na base. A R04 anotaria a mesma coisa (`sinal_risco = sim`) e não traria informação nova. Isso é a **refração**.

**25. No golpe do falso parente, por que o sistema pergunta sobre a ligação e não sobre o contato salvo?**
Porque o motor pergunta o que a regra de **maior prioridade** precisa. A R07 (prioridade 90) precisa de `confirmou_por_ligacao`. As regras que usam `contato_salvo` têm prioridade 50, então nem chegam a ser consultadas.

**26. Qual a diferença entre o palpite do modelo e a decisão do sistema?**
O palpite (`previsao`) é só **um fato** entre vários. A **decisão** (`acao`) sai das regras, que juntam o palpite com as pistas do texto e as respostas do usuário. Por isso a mesma previsão "spam" pode virar SILENCIAR (R13) ou ARQUIVAR (R14).

**27. O que significa "proporção na folha: 100%"?**
É a porcentagem de exemplos de treino daquela folha da árvore que eram da classe prevista. **Não** é uma probabilidade garantida de acerto (a Aula 12 alerta sobre isso).

---

## 9. Roteiro para apresentar (2 minutos)

1. **Dor** (20 s): números e a Dona Marta.
2. **Solução** (20 s): um consultor que cola a mensagem, responde 2 perguntas e recebe ação + explicação.
3. **Arquitetura** (40 s): AFD/ER → árvore → previsão vira fato → regras → ação.
4. **Demonstração** (30 s): o falso parente, mostrando o rastro e o "como?".
5. **Resultados e limites** (10 s): 81% na validação cruzada, 0 golpes liberados, dados simulados.

---

## 10. Glossário rápido

- **Heurística:** regra prática baseada na experiência.
- **Fato derivado:** conclusão intermediária gerada por uma regra (ex.: `sinal_risco`).
- **Refração:** uma regra não dispara duas vezes com os mesmos fatos.
- **Saliência / prioridade:** número que decide qual regra vence um conflito.
- **Estratificação:** manter a proporção das classes no treino e no teste.
- **Folha da árvore:** ponto final onde a árvore decide a classe. A "proporção na folha" **não** é uma probabilidade calibrada.
- **Falso positivo:** legítima tratada como golpe. **Falso negativo:** golpe tratado como legítima, o erro mais perigoso aqui.

---

## 11. Ética e impactos da IA (aula de 17/11)

> Arquivo completo no GitHub: `ETICA_E_IMPACTOS_IA.md`. Aqui fica o resumo para a prova.

### 11.1 O que vem das regras e o que depende do modelo

- **Só regras da equipe** (R01–R09): falso parente e clonagem. Valem **mesmo se o modelo errar**. Exemplo: o modelo chamou o pedido de token de *legítima* e a R02 + R06 bloquearam.
- **Dependem da previsão do modelo** (R10–R18): é aqui que entram os erros e vieses do dataset.

### 11.2 Os 5 aspectos éticos (decore um exemplo real de cada)

| Aspecto | Risco real no Sentinela Zap | Medida |
|---|---|---|
| **Privacidade e dados** | A mensagem colada pode ter **CPF, chave Pix e nomes** de terceiros | Não grava nada, só 2 perguntas, dataset 100% simulado. Proposta: avisar para apagar dados antes de colar |
| **Viés e equidade** | Mensagem de **professor com link** da aula → SILENCIAR E DENUNCIAR SPAM. *"Mãe, vou chegar mais tarde"* → o modelo diz golpe | A **R11** impede o bloqueio sem sinal de risco. Proposta: testar **pares contrafactuais** (formal × informal) |
| **Transparência** | "Proporção na folha: 100%" pode parecer **certeza** | A saída separa previsão do modelo, fatos e rastro, com "Como?", "Por quê?" e o aviso final |
| **Supervisão e responsabilidade** | **4 golpes de investimento** ("Invista R$ 200…") viram spam e, de contato salvo, recebem **ARQUIVAR**, porque "invista" não está na lista | A **pessoa decide** (o sistema não age sozinho), há INCONCLUSIVO e "não sei". Proposta: incluir verbos de investimento |
| **Impacto social** | O filho que teve o **celular roubado de verdade** → a mãe não consegue ligar → a R07 manda BLOQUEAR E DENUNCIAR | A R07 é **condicional** ("se não for ela…"), a R09 manda NÃO PAGAR AINDA e a R08 libera com cautela |

### 11.3 O dilema: bloquear e denunciar × alertar

| | Opção A: bloquear e denunciar já | Opção B: não pagar e bloquear; denunciar **depois** de confirmar |
|---|---|---|
| **Bom** | Proteção máxima e derruba números de golpistas | Evita denúncia injusta contra parente, professor ou comerciante |
| **Ruim** | Erro do modelo vira **acusação** contra quem não pode se defender | O golpista fica ativo por mais tempo |

**Sua posição:** manter a separação entre regras e modelo e a R09. Mudar a denúncia para **depois da verificação**, porque **quem é denunciado não usa o sistema e não pode contestar**.

### 11.4 Artigo e vídeo (as duas fontes da aula)

- **Artigo:** LACERDA JÚNIOR (2026), *Da proibição à governança*, Revista Foco. O debate saiu da "fraude" para **políticas graduadas**: o uso é permitido conforme a finalidade, a **declaração de uso** e a **responsabilização humana**. Proibir tudo orienta mal, e liberar sem critério joga o risco no aluno e no professor. A matriz tem 5 movimentos: **definir, ensinar, redesenhar, proteger e acompanhar**.
- **Vídeo:** IEA-USP, *O Estado da Arte em Ética na IA* (9/6/2026), **Steven Gouveia, de 06:07 a 06:58**. Ele conclui que a ética entra **desde o primeiro traço** do sistema, e não como acabamento.

| Nível | O que é | No Sentinela Zap |
|---|---|---|
| **Micro** | O sistema é exato, justo, transparente? | 0 golpes liberados, 432 combinações verificadas |
| **Meso** | As **instituições e os incentivos** | A plataforma que recebe a denúncia e pode restringir números |
| **Macro** | Como ficam **agência, conhecimento e responsabilidade** | Quem responde por uma denúncia injusta? |

### 11.5 Declaração de uso de IA (dois campos do modelo da professora)

- **IA incorporada ao projeto:** árvore de decisão + 18 regras + AFD e regex. Recebe o texto e 2 respostas, gera ação, recomendação e explicação. Verificada com testes, validação cruzada e as 432 combinações. A revisão cabe à pessoa usuária e ao autor.
- **IA utilizada na elaboração:** o Claude ajudou na estratégia, em partes do código, nas mensagens simuladas e nos textos. Você revisou as regras, rodou no Colab, conferiu os números e as fontes. **Declarar o uso** é exatamente o que o artigo defende.

### 11.6 Perguntas prováveis de ética

**28. Seu sistema pode prejudicar alguém? Dê um exemplo.**
Sim. Uma mensagem legítima de professor com link, vinda de número desconhecido, recebeu SILENCIAR E DENUNCIAR SPAM. Uma denúncia injusta pode restringir o número de quem só estava trabalhando.

**29. Quem é responsável se a recomendação estiver errada?**
A decisão final é da pessoa usuária, porque o sistema só recomenda. As regras são responsabilidade do autor, como engenheiro do conhecimento, que deve revisá-las. Para uso real, elas seriam validadas por especialistas antifraude.

**30. Que dados o sistema usa? Dá para usar menos?**
O texto colado e 2 respostas. O modelo usa só 14 contagens, não o texto em si, então o texto nem precisaria ser guardado. Nada é gravado e o dataset é simulado.

**31. Como o usuário sabe que recebeu uma saída de IA?**
A saída mostra separado o que é **previsão do modelo**, o que são **fatos do texto** e quais **regras dispararam**, com a explicação "Como?" e o aviso de que a decisão é humana.

**32. O que são os níveis micro, meso e macro?**
São os níveis da fala de Steven Gouveia no vídeo do IEA-USP. **Micro** é o sistema em si (exato, justo, transparente). **Meso** são as instituições e os incentivos, como a plataforma que recebe a denúncia. **Macro** é como mudam agência, conhecimento e responsabilidade.

---

## 12. Ficha de avaliação A1 (o que você preencheu)

**Descrição do SE:** sistema especialista híbrido que ajuda a decidir o que fazer com uma mensagem suspeita de WhatsApp. Fatos de entrada (s/n/?): `troca_numero`, `pede_dinheiro`, `pede_codigo`, `tem_link` (AFD + regex), `previsao` (árvore) e as perguntas `contato_salvo` e `confirmou_por_ligacao`. Caso: *"Oi mãe, troquei de número, me faz um pix"* + não confirmou → **BLOQUEAR E DENUNCIAR**.

**Base de conhecimento (2 regras):**

- **R01:** SE `troca_numero` = sim E `pede_dinheiro` = sim ENTÃO `padrao_falso_parente` = sim.
- **R07:** SE `padrao_falso_parente` = sim E `confirmou_por_ligacao` = não ENTÃO ação = BLOQUEAR E DENUNCIAR.

**Motor de inferência:** encadeamento para frente com prioridade (100 → 95 → 90 → 50). Os fatos acionam a R01. Para aplicar a R07, o motor pergunta a confirmação ("Por quê? A R07 precisa desse fato"). Com "não", conclui BLOQUEAR. O motor registra o rastro e preserva o "?" como desconhecido, e nesse caso a R09 recomenda NÃO PAGAR AINDA.

> **Se ela perguntar sobre encadeamento para trás** (o exemplo da ficha usa esse termo): *"O motor é para frente, mas só pergunta ao usuário o fato de que uma regra precisa. Partir da regra para descobrir o fato que falta é um comportamento de encadeamento para trás."*

