**UDF** Centro Universitário

Projeto final · Inteligência Artificial e LFA · 

# Ética e Impactos da Inteligência Artificial: Sentinela Zap

Análise ética do sistema especialista **Sentinela Zap**: quem pode ser afetado por suas recomendações, que medidas a equipe adota e como a inteligência artificial foi usada e verificada.

| | |
|---|---|
| **Aluno** | Luis Fernando Vieira Borges (Turma N1) |
| **Projeto** | Sentinela Zap: sistema especialista híbrido para filtrar mensagens indesejadas no WhatsApp |
| **Repositório** | github.com/luis-fernando061/LFA → `Projeto_Final_Sentinela_Zap` |
| **Base da análise** | Roteiro "Ética e Impactos da Inteligência Artificial" (aula de 17/11/2026) e resultados reais do notebook (seções 12 a 16) |

---

## 1. Leitura e vídeo

### Artigo científico · agosto de 2026

**LACERDA JÚNIOR, Francisco Pires de.** *Da proibição à governança: uso ético da inteligência artificial generativa na educação superior — uma revisão integrativa (2023–2026).* Revista Foco, v. 19, n. 8, 2026.

**Ideia central usada nesta análise.** O título resume a tese do artigo: a resposta institucional à IA generativa deixa de ser **proibir** o uso e passa a ser **governar** o uso, com regras claras, declaração e responsabilidade de quem usa.

**Questão de integridade acadêmica que a equipe deve considerar.** Neste projeto, a IA generativa não serviu apenas para "revisar o texto". Ela ajudou a escrever o código, a **redigir as 150 mensagens simuladas do dataset** e a documentação. Isso cria três perguntas de integridade:

1. **Autoria.** O que é contribuição do aluno e o que é contribuição da ferramenta? A resposta está na declaração da seção 4: as escolhas, as verificações e a responsabilidade são do aluno.
2. **Circularidade da avaliação.** Se as mensagens de teste foram redigidas com a mesma ferramenta que ajudou a projetar as regras, a acurácia de 76,3% pode ser **otimista**. Ela mede o quanto o sistema reconhece um estilo de escrita parecido com o do próprio dataset, e não golpes reais. Por isso o trabalho declara que o dataset é simulado e que os números demonstram o **método**, não a eficácia no mundo real.
3. **Verificação humana.** Governar o uso significa que nada gerado pela ferramenta entra no trabalho sem conferência. No projeto, isso foi feito com testes automáticos (`assert`), execução completa no Google Colab e revisão das regras contra o código.

> **A preencher pelo aluno antes da entrega:** anotar aqui a página ou o trecho do artigo que sustenta a ideia de "governança" usada acima. A equipe não conseguiu acessar o texto completo pela internet (o site da revista bloqueou o acesso automático).

### Vídeo · junho de 2026

**INSTITUTO DE ESTUDOS AVANÇADOS DA USP.** *O Estado da Arte em Ética na Inteligência Artificial* (lançamento do livro *The Palgrave Handbook on the Ethics of Artificial Intelligence*). São Paulo, 9 jun. 2026. Gravação de 2h01. Participantes: Veridiana Cordeiro (abertura), Steven Gouveia (Universidade do Porto, apresentação geral do livro e das linhas de pesquisa em ética na IA), João Cortese (comentários) e Mateus Henrique Amorim (moderação).

**Trecho selecionado:** minutagem **[preencher: hh:mm a hh:mm]**, sobre impactos sociais ou responsabilidades institucionais da IA.

**Ideia que levarei ao debate.** Quando um sistema de IA recomenda uma ação que atinge **outra pessoa**, a responsabilidade não pode ficar "na máquina". No Sentinela Zap, a ação **DENUNCIAR** atinge o remetente da mensagem, que não usa o sistema, não foi consultado e não tem como contestar. Quem responde se a denúncia for injusta? A equipe que escreveu a regra, a pessoa que seguiu a recomendação ou a plataforma que recebeu a denúncia?

> **A preencher pelo aluno antes da entrega:** assistir ao trecho escolhido, anotar a minutagem exata e ajustar a frase acima ao que foi dito no vídeo.

---

## 2. Análise ética do projeto

### 2.1 O projeto em um parágrafo

O **Sentinela Zap** ajuda uma pessoa a decidir o que fazer com uma mensagem suspeita recebida no WhatsApp. O problema é real: o Brasil registrou **2.261.055 casos de estelionato em 2025**, e **84%** de quem recebeu tentativas de golpe cita aplicativos de mensagem. O público principal são pessoas com pouca familiaridade digital, como a persona Dona Marta, 63 anos, que recebe o golpe do falso parente. A pessoa **cola a mensagem**, responde no máximo **duas perguntas** (se o número está salvo e se confirmou o pedido por ligação) e recebe uma **ação**, uma **recomendação** e uma **explicação**. As saídas afetam três grupos: a **pessoa usuária**, que pode perder dinheiro ou deixar de ajudar alguém; o **remetente da mensagem**, que pode ser denunciado; e a **família** da pessoa usuária. A IA participa em dois pontos: na **extração de atributos** (um AFD e expressões regulares, que são regras formais e não aprendem) e no **modelo supervisionado** (uma árvore de decisão treinada com 150 mensagens rotuladas), cuja previsão entra como fato no sistema especialista.

### 2.2 O que vem das regras da equipe e o que depende do modelo

| Origem da recomendação | Regras | Ações possíveis | Depende da previsão do modelo? |
|---|---|---|---|
| **Somente regras da equipe** (padrões de golpe conhecidos, prioridade 95 e 90) | R01 + R07, R08, R09 (falso parente) · R02 + R06 (clonagem) | BLOQUEAR E DENUNCIAR · LIBERAR COM CAUTELA · NÃO PAGAR AINDA | **Não.** Valem mesmo se o modelo errar. Exemplo real: o modelo classificou um pedido de token como *legítima* e as regras R02 e R06 bloquearam |
| **Regras que usam a previsão do modelo** (prioridade 50) | R10 a R18, com o fato derivado `sinal_risco` (R03, R04, R05) | BLOQUEAR E DENUNCIAR · VERIFICAR · SILENCIAR E DENUNCIAR SPAM · ARQUIVAR · LIBERAR | **Sim.** A previsão `golpe`, `spam` ou `legitima` é uma das condições |
| **Nenhuma regra conclui** | – | INCONCLUSIVO: "não clique, não pague e peça ajuda a alguém de confiança" | Não |

**Consequência ética:** as recomendações **mais graves e mais confiáveis** (falso parente e clonagem) vêm de conhecimento explícito da equipe, com origem documentada (WhatsApp, Febraban, Serasa). As recomendações que dependem do modelo herdam os **erros e vieses do dataset**, e é nelas que estão os principais riscos analisados a seguir.

### 2.3 Evidências usadas na análise

Os números abaixo vêm da execução real do notebook, no conjunto de teste (38 mensagens que o modelo nunca viu), simulando que a pessoa **não confirmou** por ligação:

| O que aconteceu no teste | Número desconhecido | Contato salvo |
|---|---|---|
| Golpes **liberados** | 0 de 12 | 0 de 12 |
| Golpes com alerta (BLOQUEAR, VERIFICAR ou NÃO PAGAR AINDA) | 8 de 12 | 8 de 12 |
| Golpes de **falso investimento** tratados como spam | 4 de 12 → SILENCIAR E DENUNCIAR SPAM | 4 de 12 → **ARQUIVAR** |
| Legítimas **bloqueadas** | 0 de 13 | 0 de 13 |
| Legítima com link de aula tratada como spam | 1 → **SILENCIAR E DENUNCIAR SPAM** | 1 → ARQUIVAR |
| Legítima familiar tratada como golpe pelo modelo | 1 → VERIFICAR (a R11 impediu o bloqueio) | 1 → VERIFICAR |

### 2.4 Análise dos cinco aspectos

Todos os cinco aspectos **se aplicam** ao Sentinela Zap, porque o sistema processa mensagens pessoais, usa um modelo treinado com rótulos escritos pela equipe e recomenda ações que afetam terceiros.

**Legenda:** ✅ medida já implementada no projeto · ➡️ medida proposta pela equipe.

#### Privacidade e dados

| | |
|---|---|
| **Quais dados são usados** | (1) O **texto colado** pela pessoa. (2) Duas respostas sim/não/não sei: `contato_salvo` e `confirmou_por_ligacao`. (3) Para treinar, **150 mensagens simuladas**, sem pessoas reais. O sistema **não** pede número de telefone, nome, foto nem acesso aos contatos. |
| **Podem identificar pessoas?** | **Sim, o texto colado pode.** Mensagens de golpe costumam trazer **chave Pix, CPF, nome do "filho"**, valores e links. Se a pessoa colar uma conversa real, o texto contém dados pessoais de terceiros, que pela LGPD (Lei nº 13.709/2018) exigem finalidade e cuidado. No Google Colab, o texto é processado em servidores do Google. |
| **Situação concreta** | Dona Marta cola: *"Oi mãe, é o Pedro, troquei de número, faz um pix pra chave 123.456.789-00"*. O CPF que serve de chave Pix pode ser de uma **pessoa usada como "laranja"** ou de alguém que teve os dados roubados. |
| **Quem pode ser afetado** | A pessoa usuária, o remetente e terceiros citados na mensagem (titular da chave Pix, parentes mencionados). |
| **É possível alcançar a finalidade com menos dados?** | **Sim, e o projeto já faz isso.** O modelo não usa o texto em si, e sim **14 contagens** (quantos links, quantas palavras de urgência etc.). Depois da extração, o texto não precisaria ser guardado. |
| **Medidas adotadas** | ✅ A função `analisar_mensagem` **não grava** nenhuma mensagem nem resposta. ✅ Minimização: só 2 perguntas, feitas apenas quando uma regra precisa da resposta. ✅ Dataset **100% simulado**, declarado no notebook e no README. |
| **Medidas propostas** | ➡️ Aviso antes da análise: "apague CPF, chave Pix e nomes antes de colar; o sistema não precisa deles". ➡️ Se o projeto passar a usar mensagens reais para treino: anonimizar (trocar nomes, números e chaves por marcadores como `<CHAVE_PIX>`) e coletar apenas com consentimento. ➡️ Versão que roda localmente, sem enviar o texto a servidores externos. |

#### Viés e equidade

| | |
|---|---|
| **De onde o viés pode vir** | (1) **Rótulos**: quem decidiu o que é golpe, spam ou legítima foi a equipe, com a sua visão de golpe. (2) **Dados**: as 150 mensagens foram escritas em português padrão, com a mesma "voz". (3) **Regras**: as expressões regulares têm **vocabulário fixo**. |
| **Situação concreta 1 (observada no teste)** | *"Bom dia! Segue o link da aula de hoje: https://meet.google.com/…"*, de número desconhecido, recebeu **SILENCIAR E DENUNCIAR SPAM**. O modelo associou "link + saudação" a spam. |
| **Quem pode ser afetado** | **Professores, escolas, clínicas, entregadores e pequenos serviços** que mandam links legítimos para quem ainda não salvou o número. Uma denúncia injusta pode levar à restrição do número de quem só estava trabalhando. |
| **Situação concreta 2 (observada no teste)** | *"Mãe, vou chegar mais tarde hoje, tenho reunião até as 19h."* foi prevista como **golpe** pelo modelo, por causa do tom familiar, que é o mesmo que o golpe do falso parente imita. A **R11** impediu o bloqueio e mandou **VERIFICAR**. |
| **Quem pode ser afetado** | **Famílias**, em especial filhos que trocam de chip com frequência ou usam o celular de outra pessoa. A conversa familiar legítima passa a ser tratada com desconfiança. |
| **Situação concreta 3 (risco previsto)** | Quem escreve de forma muito informal ou regional ("vc", "pfv", "manda um pis") pode não ser reconhecido pelas expressões regulares. Isso vale para golpes, que escapam, e para mensagens legítimas, classificadas só pelo modelo. |
| **Quem pode ser afetado** | Pessoas com **menor escolaridade**, falantes de variações regionais e pessoas idosas que digitam por voz ou com erros. São justamente o público que o sistema quer proteger. |
| **Medidas já adotadas** | ✅ A **R11** foi criada depois que a mensagem "Mãe, vou chegar mais tarde" era bloqueada: sem link e sem pedido de dinheiro, o sistema **nunca bloqueia**, só manda verificar. ✅ O bloqueio pelo modelo (R10) exige **sinal de risco** concreto. ✅ Resultado: **0 de 13** legítimas bloqueadas. |
| **Como a equipe verificará** | ➡️ **Pares contrafactuais**, como no exemplo do Orientador UDF: a mesma mensagem escrita em versão formal e informal, com e sem link institucional, de contato salvo e desconhecido. A ação deve mudar só quando muda o risco real. ➡️ Medir a taxa de erro **por grupo de mensagem** (familiar, comercial, institucional, informal) e não só a acurácia geral. ➡️ Incluir no dataset mensagens legítimas de professores, clínicas e pequenos vendedores com link e Pix. |

#### Transparência

| | |
|---|---|
| **Como a pessoa sabe que recebeu uma saída de IA** | A saída mostra separadamente: **`PREVISÃO DO MODELO`** (a parte que aprendeu com dados), **`FATOS DO TEXTO`** (o que o AFD e as expressões regulares encontraram), **`RASTRO DE INFERÊNCIA`** (cada regra que disparou) e a explicação **"COMO?"**, que mostra a **origem** de cada fato: `entrada`, `usuário` ou o número da regra. |
| **Situação concreta** | A saída mostra *"PREVISÃO DO MODELO: golpe (proporção na folha da árvore: 100%)"*. Uma pessoa leiga pode entender "100%" como **certeza**. |
| **Quem pode ser afetado** | A pessoa usuária, que pode confiar demais na previsão, e o remetente, se a confiança excessiva levar a uma denúncia. |
| **Informações que ajudam a entender** | ✅ O termo usado é "proporção na folha", e o notebook explica que **não** é uma probabilidade calibrada. ✅ Toda pergunta vem com **"Por quê?"** ("pergunto porque a regra R07 precisa desse fato"). ✅ A recomendação diz **o que fazer** em linguagem simples ("ligue para o número ANTIGO"). ✅ Toda saída termina com o aviso: *"a recomendação não substitui o julgamento humano"*. |
| **Medidas propostas** | ➡️ Em uma interface real, trocar o percentual por uma frase: "o modelo encontrou padrões parecidos com golpes que conhecemos". ➡️ Mostrar no topo: "**Sugestão automática. A decisão é sua.**" ➡️ Indicar quando a ação veio de **regra de golpe conhecido** (mais confiável) ou do **modelo** (mais sujeito a erro). |

#### Supervisão e responsabilidade

| | |
|---|---|
| **Quem decide** | **A pessoa usuária.** O sistema funciona como **consultor**: ele não lê o WhatsApp, não bloqueia e não denuncia ninguém sozinho. Toda ação depende de a pessoa decidir agir. |
| **Situação concreta** | No teste, 4 golpes de **falso investimento** (*"Ganhe dinheiro fácil! Invista R$ 200 e receba R$ 2.000 em uma semana"*) foram tratados como spam. Vindos de contato salvo, receberam **ARQUIVAR**, uma ação que sugere "é só chato, não é perigoso". A causa é identificável: o verbo "invista" não está na lista de pedidos de dinheiro. |
| **Quem pode ser afetado** | A pessoa que confia no "ARQUIVAR", volta à mensagem depois e investe. Também o contato salvo, que pode estar com a conta clonada. |
| **Quem revisa uma saída inadequada** | ✅ Na hora: a própria pessoa, com as opções de responder **"não sei"** (R09) e de receber **INCONCLUSIVO** com encaminhamento a alguém de confiança. ✅ Na base: o autor, como **engenheiro do conhecimento**, responde pelas regras. A **verificação exaustiva** testa as **432 combinações** de fatos (sem contradições, sem ciclos, sem regras inalcançáveis). ➡️ Idealmente, as regras seriam validadas por **especialistas de domínio**, como equipes antifraude de bancos ou órgãos de defesa do consumidor. |
| **Como a pessoa pode corrigir dados ou contestar** | ✅ Ela pode analisar a mesma mensagem de novo com outras respostas e ver a ação mudar (seção 14 do notebook). ➡️ Botão **"esta recomendação estava errada"**, que gera um caso para a equipe revisar a base. ➡️ **O remetente não pode contestar**, porque não usa o sistema. Por isso a denúncia deve ser sempre uma decisão da pessoa e vir depois de uma verificação (ver o dilema na seção 3). |
| **Medida de manutenção** | ➡️ Revisão periódica da base quando surgirem golpes novos. Correção imediata identificada neste relatório: incluir verbos de investimento ("invista", "aplique", "rendimento garantido") nos pedidos de dinheiro e criar uma regra para **promessa de lucro**. |

#### Impacto social

| | |
|---|---|
| **Quem se beneficia** | Pessoas idosas e com pouca familiaridade digital, que ganham um "segundo olhar" antes de pagar. Famílias, porque o sistema ensina a **ligar para o número antigo**, o hábito que mais evita o golpe do falso parente. Indiretamente, a sociedade, já que cada golpe evitado reduz o lucro desse crime. |
| **Quem pode ser prejudicado se a recomendação estiver errada** | **Falso negativo:** um golpe recebe ARQUIVAR ou LIBERAR e a pessoa perde dinheiro. Foi o caso dos 4 golpes de investimento no teste. **Falso positivo:** uma mensagem legítima recebe BLOQUEAR ou DENUNCIAR. Pode ser o filho que **teve o celular roubado de verdade** e pede ajuda de um número emprestado, ou o professor denunciado como spam. |
| **Situação concreta (recomendação tratada como definitiva)** | Um filho escreve de um número emprestado depois de um assalto: *"Mãe, roubaram meu celular, troquei de número, me faz um pix pra pegar um Uber"*. A mãe não consegue ligar para o número antigo, porque o celular foi roubado, e responde "não". A **R07** recomenda **BLOQUEAR E DENUNCIAR**. Se ela tratar a saída como definitiva, deixa o filho sem ajuda numa emergência real e ainda denuncia o número de quem o socorreu. |
| **Medidas adotadas** | ✅ A recomendação da R07 é **condicional**: "Ligue para o número ANTIGO da pessoa. **Se não for ela**, bloqueie, denuncie e avise a família". ✅ A **R09** separa "não sei" de "não" e recomenda **NÃO PAGAR AINDA** em vez de bloquear. ✅ A **R08** libera **com cautela** e manda conferir o nome do titular da chave Pix. |
| **Medidas propostas** | ➡️ Sugerir outros meios de confirmação além da ligação: uma **pergunta que só o parente saberia responder** ou falar com outro familiar. ➡️ Separar "BLOQUEAR" de "DENUNCIAR" (ver o dilema na seção 3). ➡️ Junto de cada ação, uma frase educativa que ensine o padrão do golpe, para a pessoa aprender a reconhecer sozinha e não depender do sistema. |

---

## 3. Debate em sala e reflexão individual

### 3.1 Dilema ético: bloquear e denunciar, ou apenas alertar?

Hoje, três regras recomendam **BLOQUEAR E DENUNCIAR**: R06 (clonagem), R07 (falso parente sem confirmação) e R10 (o modelo prevê golpe, o número é desconhecido e há sinal de risco). A ação junta duas coisas diferentes: **bloquear** protege a pessoa usuária, enquanto **denunciar** afeta o remetente e pode levar a plataforma a restringir o número dele.

| | **Opção A: manter "bloquear e denunciar"** | **Opção B: alertar e deixar a denúncia para depois da verificação** |
|---|---|---|
| **Como funciona** | Ao reconhecer o padrão, o sistema recomenda bloquear e denunciar imediatamente. | O sistema recomenda **não pagar e bloquear**. A denúncia só é sugerida depois de a pessoa confirmar, por outro meio, que não era quem dizia ser. |
| **Consequências positivas** | Proteção máxima para quem tem menos defesa. Cada denúncia ajuda a plataforma a derrubar números de golpistas e protege **outras** possíveis vítimas. Mensagem simples, fácil de seguir. | Menos **denúncias injustas** contra parentes em emergência, professores e pequenos comerciantes. Respeita quem não pode se defender, porque o remetente não usa o sistema. |
| **Consequências negativas** | Erros do modelo viram **acusações**. No teste, o modelo errou o rótulo de 9 das 38 mensagens. A pessoa pode denunciar o número emprestado de quem socorreu o filho. | Golpistas ficam ativos por mais tempo. Uma pessoa vulnerável pode continuar a conversa "para verificar" e ser convencida pelo golpista. A recomendação fica mais longa e mais difícil de seguir. |

**Questão para o debate**, adaptada do roteiro: *se o Sentinela Zap identificar um golpe, em que condições essa informação deveria ser compartilhada com terceiros, como a plataforma, o banco, a família ou a polícia?* Proposta da equipe: **somente por decisão da pessoa usuária, com o mínimo de dados e nunca de forma automática.** Ganham a família e as outras possíveis vítimas. Pode ser prejudicado o remetente acusado injustamente e o titular de uma chave Pix que teve os dados usados por golpistas.

**Ideia que sustenta a análise.** O artigo de Lacerda Júnior defende a passagem **"da proibição à governança"**. A Opção A se parece com a lógica da proibição: diante da suspeita, corta tudo, inclusive o remetente. A Opção B se parece com a governança: a decisão fica com uma pessoa informada, há etapas de verificação e a responsabilidade sobre a ação mais grave, a denúncia, fica clara. O vídeo do IEA-USP reforça esse ponto ao tratar das **responsabilidades institucionais** da IA: uma ação que atinge terceiros precisa de alguém que responda por ela.

### 3.2 Reflexão individual

> **Rascunho para o aluno adaptar com as próprias palavras depois do debate.** A reflexão é individual e deve refletir a posição do aluno.

Depois da análise, eu **manteria** a separação entre regras de golpe conhecido e regras que dependem do modelo, porque ela garante que os casos mais graves, como o pedido de código e o falso parente, não dependem de um modelo que errou 9 das 38 mensagens do teste. Também manteria a R09, que trata o "não sei" de forma diferente do "não". Eu **mudaria** a ação "BLOQUEAR E DENUNCIAR" das regras R07 e R10 para "**NÃO PAGUE E BLOQUEIE; denuncie depois de confirmar por outro meio**". Também trocaria o "SILENCIAR E DENUNCIAR SPAM" da R13 por "SILENCIAR" quando a mensagem tiver link de uma instituição, porque o nosso próprio teste mostrou um professor sendo denunciado como spam. Para os golpes de investimento que receberam ARQUIVAR, eu acrescentaria os verbos de investimento ao pedido de dinheiro e uma regra de promessa de lucro. O argumento que mais influenciou minha posição foi perceber que **quem é denunciado não usa o sistema e não pode contestar**, junto com a ideia de governança do artigo: a ação mais grave precisa de verificação humana. Para verificar se as mudanças funcionam, eu rodaria de novo a avaliação da seção 15 do notebook com **pares contrafactuais** (a mesma mensagem formal e informal, com e sem link institucional, de contato salvo e desconhecido). As metas seriam **0 legítimas denunciadas**, **0 golpes arquivados ou liberados** e uma taxa de erro parecida entre os grupos de mensagem (familiar, comercial, institucional). Também rodaria de novo a verificação exaustiva, para garantir que as regras novas não criam contradições.

---

## 4. Declaração de uso de sistemas de IA

### IA incorporada ao projeto

O projeto utiliza **um sistema especialista com 18 regras SE…ENTÃO e motor de inferência com encadeamento para frente, integrado a um modelo de aprendizado supervisionado (árvore de decisão `DecisionTreeClassifier`, scikit-learn 1.6.1, com hiperparâmetros escolhidos por `GridSearchCV` só no conjunto de treino). Os atributos do texto são extraídos por um autômato finito determinístico (AFD) e por expressões regulares** para **recomendar o que a pessoa deve fazer diante de uma mensagem suspeita de WhatsApp e explicar o porquê**. O sistema recebe **o texto colado pela pessoa e até duas respostas (se o número está salvo e se o pedido foi confirmado por ligação: sim, não ou não sei)** e gera **uma ação (LIBERAR, LIBERAR COM CAUTELA, VERIFICAR, NÃO PAGAR AINDA, ARQUIVAR, SILENCIAR E DENUNCIAR SPAM, BLOQUEAR E DENUNCIAR ou INCONCLUSIVO), uma recomendação em linguagem simples, o rastro das regras disparadas e a explicação "Como?"**. A equipe verificou seu funcionamento por meio de **divisão estratificada de treino e teste (112/38), acurácia no teste (76,3%), matriz de confusão, validação cruzada em 5 partes (81,3% ± 4,0%), os três testes obrigatórios com verificação automática (positivo, negativo e informação ausente), três casos novos, comparação mudando um único fato, avaliação do sistema híbrido (0 de 12 golpes liberados e 0 de 13 legítimas bloqueadas), verificação exaustiva de 432 combinações de fatos (sem contradições, ciclos ou regras inalcançáveis), equivalência entre o AFD e a expressão regular em 908 tokens (0 divergências) e execução completa no Google Colab**. As limitações identificadas incluem **o dataset pequeno e simulado (150 mensagens escritas pela equipe), o vocabulário fixo das expressões regulares, golpes de falso investimento tratados como spam (4 de 12 no teste), uma mensagem legítima de aula com link tratada como spam, a "proporção na folha" que não é uma probabilidade calibrada, a ausência de análise de áudios e imagens e o fato de o sistema não ler o WhatsApp automaticamente. Exigem revisão humana todas as recomendações de denúncia, os casos INCONCLUSIVOS e os pedidos de dinheiro de contatos salvos**. A revisão cabe **à pessoa usuária, que toma a decisão final, e ao autor do projeto, Luis Fernando Vieira Borges, como engenheiro do conhecimento responsável por manter e revisar as regras e o modelo. Para uso real, as regras deveriam ser validadas por especialistas em prevenção a fraudes**.

### IA utilizada na elaboração do trabalho

Utilizei **o Claude (Anthropic, modelo Claude Opus 5.5), por meio do aplicativo Claude Code** para **estruturar o projeto, escrever e depurar o código Python do notebook, redigir as 150 mensagens simuladas do dataset, redigir o README, o guia de estudo e esta análise ética, pesquisar as fontes citadas, testar o notebook no Google Colab e publicar os arquivos no GitHub**. Aproveitei **a arquitetura do sistema, o código do motor de inferência, do AFD e das regras, o dataset simulado, os textos de documentação e a organização desta análise** após **executar o notebook inteiro localmente e no Google Colab sem erros, conferir os testes automáticos (os quatro ✅), revisar cada uma das 18 regras contra o código, conferir os números citados contra a saída real do notebook e corrigir os problemas encontrados durante os testes. Dois exemplos: a criação da regra R11, depois que uma mensagem legítima familiar era bloqueada, e a criação do atributo de pedido de dinheiro, depois que um pedido de Pix de um contato salvo era liberado. As ideias atribuídas ao artigo e ao vídeo na seção 1 foram conferidas por mim nas fontes originais**. Assumo a responsabilidade pelas escolhas, verificações e conteúdo da entrega.

---

## Referências

- LACERDA JÚNIOR, F. P. de. Da proibição à governança: uso ético da inteligência artificial generativa na educação superior — uma revisão integrativa (2023–2026). *Revista Foco*, v. 19, n. 8, 2026.
- INSTITUTO DE ESTUDOS AVANÇADOS DA USP. *O Estado da Arte em Ética na Inteligência Artificial*. Vídeo, 2h01. São Paulo, 9 jun. 2026. Disponível em: iea.usp.br/midiateca/video/videos-2026/o-estado-da-arte-em-etica-na-inteligencia-artificial.
- BRASIL. Lei nº 13.709, de 14 de agosto de 2018. Lei Geral de Proteção de Dados Pessoais (LGPD).
- UDF. Roteiro "Ética e Impactos da Inteligência Artificial", aula de 17/11/2026, Profa. Kadidja Valéria.
- Sentinela Zap: notebook `Sentinela_Zap_Sistema_Especialista.ipynb`, seções 12 a 16 (testes, avaliação híbrida e verificação da base).
- Fórum Brasileiro de Segurança Pública, Anuário 2026 (dados de 2025); Cetic.br, pesquisa sobre golpes por aplicativos de mensagem.

<p class="rodape">Luis Fernando Vieira Borges · Inteligência Artificial e LFA · UDF</p>
