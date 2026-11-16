# LFA: Linguagens Formais e Autômatos

Atividades da disciplina de **Linguagens Formais e Autômatos / Inteligência Artificial**, UDF Centro Universitário, com a Profa. Kadidja Valéria.

**Aluno:** Luis Fernando Vieira Borges (Turma N1)

---

## ⭐ Projeto final: Sentinela Zap

[![Abrir no Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/luis-fernando061/LFA/blob/main/Projeto_Final_Sentinela_Zap/Sentinela_Zap_Sistema_Especialista.ipynb)

**Sistema especialista híbrido que filtra mensagens indesejadas no WhatsApp.** Um modelo supervisionado (árvore de decisão) classifica a mensagem como legítima, spam ou golpe. Essa previsão vira um fato para 18 regras SE…ENTÃO, que decidem a ação e explicam o porquê. Os atributos do texto são extraídos com um **AFD** e com **expressões regulares**.

- 📂 Pasta: [`Projeto_Final_Sentinela_Zap`](Projeto_Final_Sentinela_Zap). O README da pasta explica cada arquivo e cada seção do notebook.
- ▶️ Para testar: clique em **Abrir no Colab** acima e depois em **Ambiente de execução → Executar tudo**.

---

## 📁 O que tem em cada pasta

Em ordem de conteúdo da disciplina:

| # | Pasta | Assunto | O que tem dentro |
|---|---|---|---|
| 1 | [`Liguagens_formais`](Liguagens_formais) | Linguagens formais (Aulas 1 e 2) | Exercícios sobre alfabeto (Σ), cadeias, palavra vazia (ε), prefixos e sufixos, Σ*, linguagens e gramáticas |
| 2 | [`Unidade2_gramaticasFormais`](Unidade2_gramaticasFormais) | Gramáticas formais | Exercícios comentados de alfabeto, linguagens e gramáticas, e exercícios de gramáticas regulares e livres de contexto (GLC) |
| 3 | [`Expressoes_Regulares`](Expressoes_Regulares) | Expressões regulares (Aula 5) | Atividade de reconhecimento e construção de padrões, com os testes `preg_match` e `preg_grep` em PDF |
| 4 | [`Aula_06_atividade-leitura-artigo-ferramentas-lfa.md`](Aula_06_atividade-leitura-artigo-ferramentas-lfa.md) | Ferramentas para aprender LFA (Aula 6) | Leitura e discussão do artigo de Mioni e Barbosa sobre ferramentas de ensino de LFA, com as respostas em PDF |
| 5 | [`Automatos_finitos_deterministicos`](Automatos_finitos_deterministicos) | Autômatos finitos determinísticos (AFD) | Lista de exercícios: identificar, construir e testar AFDs estado por estado |
| 6 | [`atividade_maquina_de_turing`](atividade_maquina_de_turing) | Máquinas de Turing | Atividade e 3 testes: entradas aceitas `0011` e `000111`, entrada rejeitada `00111` |
| 7 | [`ATIVIDADE REMOTA — MÁQUINAS DE TURING`](<ATIVIDADE REMOTA — MÁQUINAS DE TURING>) | Máquinas de Turing (atividade remota) | Mesma atividade entregue na modalidade remota, com respostas e os 3 testes em PDF |
| 8 | [`Atividade_Agentes_Inteligentes`](Atividade_Agentes_Inteligentes) | Agentes inteligentes (IA) | Exercícios 2.3, 2.4 e 2.5 do Capítulo 2: racionalidade, ambientes de tarefa e modelo PEAS |
| 9 | [`Projeto_Final_Sentinela_Zap`](Projeto_Final_Sentinela_Zap) | **Projeto final**: sistemas especialistas e aprendizado de máquina (Aulas 12 e 13) | Notebook para o Colab, dataset com 150 mensagens, README e guia de estudo |

---

## Como o projeto final usa o conteúdo das outras pastas

| Conteúdo | Onde aparece no Sentinela Zap |
|---|---|
| Linguagens formais e alfabeto (pastas 1 e 2) | A linguagem L = {`http://`, `https://`, `www.`} · Σ⁺ dos links |
| Expressões regulares (pasta 3) | Detecção de pedido de dinheiro, pedido de código, troca de número e urgência |
| AFD (pasta 5) | O autômato M = (Q, Σ, δ, q0, F) que reconhece links, com prova de equivalência com a regex |
| Agentes inteligentes (pasta 8) | O sistema percebe a mensagem, raciocina com as regras e age recomendando uma ação |
