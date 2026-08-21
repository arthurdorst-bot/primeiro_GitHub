\# Revisão de PR — Validador de CPF



\## Ferramenta e modelo

Usei o Claude (Claude.ai), com a skill própria `revisao-de-pr` criada e salva

como Habilidade. A skill roda `git diff` contra a base, analisa cada trecho

alterado e verifica os achados de verdade (ex: `node -c` no JS, testes

funcionais simulando o DOM) antes de listar no relatório.



\## Pergunta que não tinha pensado

Ao criar a skill, a ferramenta perguntou onde os comandos de verificação

(build/teste/lint) deveriam rodar. Não tinha pensado nisso — como o projeto

é um único `index.html` sem build, tive que decidir explicitamente que a

verificação seria manual/via navegador ou checagem de sintaxe do JS embutido,

já que não existe pipeline configurado.



\## Achado

A skill original não pegou, mas eu percebi ao ler o código: os dois blocos

que calculam os dígitos verificadores do CPF são quase idênticos (mesmo

padrão de loop, mudando só o range e os pesos). Isso é risco de bug — se

alguém corrigir um dos blocos no futuro e esquecer o outro, os dois dígitos

passam a divergir silenciosamente. Atualizei a skill pra checar duplicação de

lógica explicitamente, e ela passou a apontar esse tipo de achado.

