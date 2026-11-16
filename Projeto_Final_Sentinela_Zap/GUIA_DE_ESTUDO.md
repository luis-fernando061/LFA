# 📘 Guia de Estudo: Sentinela Zap

> Resumo para **estudar e explicar o projeto na prova**. Cada conceito das Aulas 12 e 13 aparece ligado a **onde ele está no nosso projeto**.

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
