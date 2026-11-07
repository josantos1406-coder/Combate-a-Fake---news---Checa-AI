# CHECA AI

Plataforma de apoio à verificação de informações e ao combate à desinformação. O front-end terá uma interface de chatbot para orientar as pessoas a investigar notícias, mensagens e afirmações suspeitas com pensamento crítico.

## Objetivo

Promover o acesso responsável à informação e incentivar a checagem de fontes, datas, contexto e evidências antes de compartilhar um conteúdo.

## Interface do chatbot

O front-end deve apresentar uma conversa simples e acolhedora com o CHECA AI. A pessoa poderá digitar uma pergunta ou selecionar uma sugestão de pergunta frequente. As respostas devem ser claras, educativas e indicar próximos passos para investigar o conteúdo.

### Elementos da tela

- Cabeçalho com o nome e a identificação do CHECA AI.
- Mensagem de boas-vindas explicando como iniciar a conversa.
- Área de conversa com mensagens da pessoa e respostas do chatbot.
- Sugestões clicáveis de perguntas frequentes.
- Campo de texto para escrever uma pergunta e botão para enviar.
- Apresentação de fontes e links quando forem fornecidos pela resposta.
- Mensagem de encerramento educativa.

### Sugestão de boas-vindas

> Olá! Eu sou o CHECA AI. Posso ajudar você a entender como verificar notícias e informações suspeitas. Envie sua dúvida ou escolha uma das perguntas abaixo. Antes de compartilhar, verifique!

### Perguntas frequentes

- O que são fake news?
- Como saber se uma notícia é falsa?
- Posso enviar uma notícia para verificar?
- Quais fontes você usa?
- O que são deepfakes?
- Como evitar compartilhar notícias falsas?
- O CHECA AI pode cometer erros?

## Capacidades do chatbot

O chatbot usa perguntas e respostas previamente cadastradas para orientar o usuário. Ele pode:

- responder dúvidas frequentes sobre fake news e desinformação;
- explicar como conferir fontes, datas, contexto e evidências;
- orientar sobre montagens, manipulações e conteúdos fora de contexto em imagens e vídeos;
- indicar fontes e iniciativas de checagem;
- compartilhar dicas de segurança digital e de uso responsável da informação;
- encerrar a conversa com uma mensagem educativa.

Quando a pessoa enviar uma notícia, mensagem ou afirmação suspeita, o chatbot deve oferecer orientações para investigar a origem, o contexto e as evidências. A resposta não deve declarar que o conteúdo é verdadeiro ou falso sem base suficiente.

## Fontes de consulta

O material de perguntas e respostas menciona estas iniciativas:

- [Aos Fatos](https://www.aosfatos.org/)
- [Boatos.org](https://www.boatos.org/)
- [G1 Fato ou Fake](https://g1.globo.com/fato-ou-fake/)
- [Agência Lupa](https://lupa.uol.com.br/)

As fontes devem ser consultadas diretamente para confirmar informações e acessar o contexto completo de cada checagem. O front-end deve exibir links quando eles estiverem disponíveis na resposta cadastrada.

## Fluxo da conversa

1. A pessoa inicia uma conversa e recebe as boas-vindas.
2. Envia uma pergunta ou apresenta uma informação que deseja investigar.
3. O chatbot identifica a pergunta e apresenta uma resposta cadastrada ou uma orientação adequada.
4. Quando pertinente, a resposta indica fontes e caminhos para conferir as evidências.
5. A pessoa é incentivada a consultar as fontes originais antes de compartilhar.

## Limitações e uso responsável

- O CHECA AI é uma ferramenta de apoio. Não substitui apuração jornalística nem consulta às fontes originais.
- As respostas automáticas podem ter limitações ou estar desatualizadas.
- Não encontrar uma informação nas referências disponíveis não significa que ela seja verdadeira ou falsa.
- No material atual, o chatbot não analisa imagens diretamente. Se a pessoa enviar uma imagem, a interface deve explicar essa limitação e convidá-la a descrever a imagem ou o contexto para receber orientações de verificação.
- O chatbot deve evitar apresentar uma conclusão automática como prova definitiva.

## Material de referência

O arquivo de apoio `CHATBOT.xlsx` contém duas planilhas:

- **PERGUNTAS**: perguntas frequentes e respectivas respostas automáticas.
- **NECESSARIOS**: categorias de atendimento e indicações de quando cada categoria deve ser utilizada.

As respostas e categorias dessas planilhas devem orientar o conteúdo e o comportamento do chatbot no front-end.

## API de denúncias de notícias

O backend da plataforma usa Node.js, Express e SQLite para registrar denúncias, manter a contagem de cada notícia e fornecer um ranking. A API será integrada ao front-end junto à experiência do chatbot.

### Endereço e execução local

Por padrão, a API fica em `http://localhost:3000`.

```bash
npm install
npm start
```

O servidor usa Node.js 22.13 ou superior e o módulo nativo `node:sqlite`. A porta pode ser configurada pela variável `PORT` (padrão `3000`); o arquivo do banco é configurado por `DB_FILE` (padrão `denuncias.db`).

### Rotas para o front-end

#### Listar notícias — `GET /noticias`

Parâmetros de consulta:

| Parâmetro | Padrão | Descrição |
| --- | --- | --- |
| `ordenar` | `denuncias` | `denuncias`, `recentes` ou `titulo` |
| `ordem` | `desc` | `asc` ou `desc` |
| `limite` | `20` | Itens por página, até `100` |
| `pagina` | `1` | Página solicitada |

Valores inválidos para `ordenar` ou `ordem` retornam `400`.

#### Criar notícia — `POST /noticias`

Rota de apoio para cadastrar uma notícia.

```json
{
  "titulo": "Título da notícia",
  "conteudo": "Conteúdo da notícia"
}
```

Retorna `201` com a notícia criada.

#### Consultar notícia — `GET /noticias/:id`

Retorna os dados da notícia, incluindo `total_denuncias`. Retorna `404` se a notícia não existir.

#### Enviar denúncia — `POST /noticias/:id/denuncias`

```json
{
  "denunciante": "ana",
  "motivo": "fake_news",
  "descricao": "Informação falsa (opcional, até 500 caracteres)"
}
```

Motivos aceitos: `fake_news`, `discurso_de_odio`, `conteudo_improprio`, `spam`, `violacao_de_direitos_autorais` e `outro`.

Retorna `201` com a denúncia registrada e o novo `total_denuncias`. A interface deve tratar os erros `400` (dados ausentes ou motivo inválido), `404` (notícia inexistente) e `409` (denunciante já registrou denúncia para essa notícia).

#### Listar denúncias de uma notícia — `GET /noticias/:id/denuncias`

Retorna `total`, contagem `por_motivo` e a lista de denúncias daquela notícia.

#### Listar motivos — `GET /denuncias/motivos`

Retorna a lista de motivos aceitos pela API. O front-end pode usar esse retorno para preencher o seletor de motivo no formulário.

#### Ranking — `GET /ranking`

Retorna as notícias mais denunciadas, com posição, identificador, título, total de denúncias e data de criação.

| Parâmetro | Padrão | Descrição |
| --- | --- | --- |
| `ordem` | `desc` | `desc` (mais denunciadas primeiro) ou `asc` |
| `limite` | `10` | Itens por página, até `100` |
| `pagina` | `1` | Página solicitada |
| `minimo` | `1` | Número mínimo de denúncias para incluir a notícia |

Em caso de empate, a notícia com menor `id` aparece primeiro.

### Comportamento esperado na interface

- Mostrar a contagem de denúncias junto à notícia e atualizar os dados após o envio bem-sucedido.
- Oferecer um formulário de denúncia com motivo e descrição opcional.
- Impedir envio incompleto no formulário e exibir as mensagens de erro da API de forma compreensível.
- Informar quando a pessoa já denunciou aquela notícia, respeitando a resposta `409`.
- Exibir o ranking com posição e total de denúncias, permitindo paginação e os filtros disponíveis.
- Tratar carregamento, lista vazia, falha de conexão e notícia inexistente.

### Regras de negócio relevantes

- Cada denunciante pode denunciar uma notícia uma única vez. A API garante unicidade pelo par notícia/denunciante.
- A gravação da denúncia e a atualização do contador ocorrem na mesma transação.
- O campo `denunciante` é texto livre na versão atual. Se houver autenticação, o backend deve usar o identificador autenticado da pessoa em vez de confiar em nomes digitados.
- Respostas de erro seguem o formato `{ "erro": "mensagem" }`. JSON malformado retorna `400`; rota inexistente retorna `404`.

### Estrutura informada do backend

```text
src/
├── server.js
├── app.js
├── db.js
├── routes/index.js
└── services/denuncias.js
test/
└── api.test.js
```

O front-end deve usar o endereço local durante o desenvolvimento e permitir configurar a URL base da API conforme o ambiente de execução.

## Público-alvo

Pessoas que desejam aprender a identificar notícias falsas, verificar informações recebidas e compartilhar conteúdos com mais responsabilidade.

## Direção do front-end

A interface deve priorizar leitura fácil, navegação simples e boa experiência em celulares e computadores. As mensagens devem diferenciar visualmente as falas do usuário e do CHECA AI. Sugestões de perguntas ajudam a pessoa a começar sem precisar conhecer os comandos do chatbot.

Tecnologias, identidade visual e integração com o backend serão definidos quando as informações correspondentes forem compartilhadas.

## Mensagem do projeto

**Antes de compartilhar, verifique!**
