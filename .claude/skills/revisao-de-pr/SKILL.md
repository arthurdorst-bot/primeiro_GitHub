\---

name: revisao-de-pr

description: Revisa mudanças de código em um repositório HTML/CSS/JavaScript vanilla (arquivo único, sem build/testes) comparando o branch atual com a main. Use SEMPRE antes de abrir um Pull Request, ao revisar o PR de um colega, ou quando o usuário pedir "revisar código", "revisar PR", "code review" ou "revisar as mudanças" neste repositório. Gera um relatório em Markdown, em português, pronto para colar como comentário de PR, separando problemas que quebram o programa de preferências de estilo, sem nunca reclamar de formatação.

\---



\# Revisão de PR (HTML/CSS/JS vanilla, sem build)



Skill para revisar apenas o que mudou em um branch, com foco no repositório

atual: um projeto estático de página única (HTML + CSS + JS vanilla), sem

gerenciador de pacotes, sem build e sem suíte de testes.



\## Quando usar



\- Antes de o usuário abrir um Pull Request (ele revisa o próprio branch).

\- Quando o usuário for revisar o PR de um colega (branch ou PR de outra pessoa).

\- Quando o usuário pedir explicitamente "revisar código", "code review" ou

&#x20; "revisar PR".



\## Antes de começar: perguntar o que faltar



Não assuma. Confirme com o usuário (só o que não estiver claro pelo contexto):



1\. \*\*Qual branch revisar\*\* e contra qual base comparar (normalmente `main`,

&#x20;  mas confirme o nome exato — pode ser `master`).

2\. \*\*De quem é o branch\*\* — o próprio usuário ou de um colega (muda o tom do

&#x20;  relatório, mas não o rigor).

3\. \*\*Onde está o repositório\*\* — path local, ou se precisa ser clonado.



Se o usuário já respondeu tudo isso na mensagem, não pergunte de novo.



\## Passo 1 — Descobrir o que mudou



Rode o diff no bash, olhando \*\*só o que mudou\*\*, nunca o arquivo inteiro:



```bash

cd <path-do-repo>

git fetch origin main

git diff origin/main...HEAD           # ou o nome real da branch base

```



Se quiser contexto de números de linha exatos por arquivo:



```bash

git diff --unified=0 origin/main...HEAD

```



Use `git log origin/main..HEAD --oneline` se for útil entender a sequência de

commits, mas a revisão em si é sobre o diff, não sobre o histórico.



\*\*Regra dura: revise apenas as linhas alteradas (adicionadas/modificadas) no

diff.\*\* Não vasculhe o arquivo inteiro procurando problemas pré-existentes que

não foram tocados nesta mudança — isso não é o que foi pedido e infla o

relatório com ruído.



\## Passo 2 — Analisar cada trecho alterado



Para cada hunk do diff, pergunte:



\- \*\*Isso quebra o programa?\*\* Ex: erro de sintaxe JS, variável não definida,

&#x20; seletor CSS que nunca casa com nada, elemento referenciado no JS que não

&#x20; existe no HTML, função chamada com assinatura errada, event listener preso

&#x20; a um elemento removido, uso de `var`/escopo que gera bug real (não estilo).

\- \*\*Isso é só preferência de estilo?\*\* Ex: nomeação, organização de pastas

&#x20; (não aplicável aqui, é arquivo único), forma de escrever algo que já

&#x20; funciona igual de bem de outro jeito.

\- \*\*Há lógica duplicada/redundante no trecho alterado?\*\* Ex: dois blocos que

&#x20; fazem o mesmo tipo de cálculo/validação com pequena variação de parâmetros

&#x20; (range, pesos, condição). Isso não é preferência de estilo: é risco real de

&#x20; bug — se um dos blocos for corrigido depois e o outro for esquecido, o

&#x20; comportamento diverge silenciosamente. Aponte como achado de nível baixo,

&#x20; citando os dois trechos duplicados e sugerindo extrair uma função comum.

\- \*\*NUNCA\*\* aponte formatação (indentação, aspas simples vs duplas, ponto e

&#x20; vírgula, espaçamento) — isso é ruído, não revisão.



Sempre que possível, \*\*verifique o achado de verdade\*\* em vez de só ler e

suspeitar:



\- Sintaxe do JS embutido: `node -c <arquivo>` (ou extraia o bloco `<script>`

&#x20; para um `.js` temporário e rode `node -c` nele).

\- Comportamento visual/funcional: abra o HTML localmente e descreva o passo a

&#x20; passo que reproduz o problema (ex: "clicar no botão X sem preencher o campo

&#x20; Y lança TypeError no console").

\- Se não for possível executar/abrir (ambiente sem browser), diga isso

&#x20; explicitamente no relatório em vez de fingir que verificou.



\## Regras de qualidade de cada achado (não negociáveis)



Todo achado listado precisa ter as três coisas abaixo. Se faltar uma, o

achado não entra no relatório:



1\. \*\*Localização exata\*\* — arquivo e linha (ou intervalo de linhas) do diff.

&#x20;  Nunca "tem um problema no JS" sem dizer onde.

2\. \*\*Como reproduzir ou confirmar\*\* — passo a passo ("abra X, clique em Y,

&#x20;  veja Z no console") ou o comando usado para confirmar (ex: saída do

&#x20;  `node -c`).

3\. \*\*Categoria clara\*\* — crítico (quebra o programa) / médio (bug real mas

&#x20;  de baixo impacto ou caso raro) / baixo (preferência, sugestão opcional).

&#x20;  Nunca misture "isso quebra" com "eu prefiriria" no mesmo item.



Se for sugerir refatoração, é obrigatório dizer \*\*qual problema concreto\*\*

ela resolve (bug, ambiguidade, comportamento inesperado). Sugestão de

refatoração sem problema associado não entra no relatório — vira, no máximo,

uma nota opcional separada, claramente marcada como "sem impacto funcional".



\## Passo 3 — Montar o relatório



Gere a resposta (chat, não arquivo, a menos que o usuário peça um arquivo)

seguindo este template:



```markdown

\## Revisão: <branch> vs <base>



\*\*Veredito:\*\* <uma linha: aprovar / aprovar com ressalvas / pedir mudanças>



\### Crítico (quebra o programa)

\- \*\*\[arquivo:linha]\*\* <descrição do problema>

&#x20; - Como reproduzir/confirmar: <passos ou comando>

&#x20; - Sugestão: <correção concreta>



\### Médio (bug real, impacto menor)

\- \*\*\[arquivo:linha]\*\* <descrição>

&#x20; - Como reproduzir/confirmar: <passos ou comando>

&#x20; - Sugestão: <correção concreta>



\### Baixo / opcional (preferência, não bloqueia)

\- \*\*\[arquivo:linha]\*\* <descrição>

&#x20; - Problema que resolve: <motivo concreto, não estético>

&#x20; - Sugestão: <correção concreta>



\### O que foi verificado

\- Rodando comando: <lista, ex: "node -c script extraído das linhas 40-90 do index.html — sem erro de sintaxe">

\- Apenas lido (não executado): <lista, ex: "lógica do formulário de contato, linhas 120-160">

```



Se uma seção não tiver achados, escreva "Nada encontrado" — não omita a

seção, para deixar claro que ela foi checada.



A seção final "O que foi verificado" é obrigatória sempre, mesmo que curta:

ela existe para o usuário saber o que teve confirmação real e o que foi

avaliação por leitura.



\## O que esta skill nunca faz



\- Reclamar de formatação (indentação, aspas, ponto e vírgula, espaçamento).

\- Listar um problema sem dizer arquivo e linha.

\- Sugerir refatoração sem apontar qual problema concreto ela resolve.

\- Revisar código fora do diff (fora do escopo da mudança).

\- Misturar "isso quebra" com "isso é gosto" no mesmo achado.

