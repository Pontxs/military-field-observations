# Sistema de Fatos Observados para Campo Básico

**Demo ao vivo:** https://pontxs.github.io/Sistema-de-Fatos-Observados-Campo/

## Dashboard

| Tela de Lançamento | Tela de Histórico |
| :---: | :---: |
| Lançar FO | Histórico |

## Motivação

Durante o Campo Básico, os instrutores de cada oficina registram Fatos Observados (FOs) dos soldados ao longo do dia, positivos e negativos, refletindo desempenho, atitude e disciplina.

O processo tradicional era manual: cada instrutor anotava em papel e, ao fim do dia, alguém consolidava tudo à mão para definir quem se destacou. Isso gerava três problemas principais:

**1. Consolidação lenta e sujeita a erros.** Com vários instrutores registrando em folhas separadas, consolidar os dados no fim do dia exigia retrabalho, transcrição manual e ficava sujeito a perdas e inconsistências.

**2. Seleção dos destaques do dia demorada.** Escolher o destaque diurno e noturno, que define quem arria e hasteia a Bandeira Nacional, exigia que alguém somasse manualmente os FOs de cada soldado ao fim de cada período. Quanto mais FOs, mais trabalhoso.

**3. Sem visão consolidada do Campo.** Não havia forma rápida de responder perguntas como: quem tem o melhor saldo geral? Qual grupamento está se destacando? Qual subunidade tem mais FOs negativos?

## A Solução

O app centraliza todos os FOs em um banco de dados na nuvem acessível de qualquer dispositivo móvel, em tempo real. Cada instrutor registra os FOs da sua oficina direto no celular, e o sistema:

- Determina automaticamente o destaque diurno e noturno de cada dia, com base no saldo de FOs (positivos menos negativos), eliminando a contagem manual
- Exibe ranking completo dos soldados por saldo, positivos ou negativos, filtrável por semana de instrução
- Identifica a honra correspondente: o destaque diurno arria a Bandeira ao fim do dia; o destaque noturno hasteia no dia seguinte
- Consolida a visão geral do Campo por soldado, grupamento e subunidade, disponível a qualquer instrutor em tempo real

Um processo que levava minutos de consolidação manual passa a ser instantâneo e visível a todos ao mesmo tempo.

## Aviso sobre a Demo

Este repositório é uma demonstração pública. A demo ao vivo conecta a um projeto Firebase dedicado a essa demonstração, então qualquer visitante pode registrar e apagar FOs de teste e ver as mudanças refletidas em tempo real.

- Todos os nomes de instrutores, unidades, grupamentos e subunidades em `index.html` são fictícios
- Os dados presentes no banco da demo são conteúdo fictício, apenas para demonstração
- O app não é destinado a uso operacional com dados reais de pessoal
- Como a demo é totalmente aberta, entradas de teste feitas por visitantes podem aparecer no histórico

Para usar o app com um banco privado próprio, siga a seção de Configuração abaixo e substitua as credenciais do Firebase pelo seu projeto.

## Funcionalidades

- Registro de FO: positivo e negativo, por soldado, grupamento, subunidade e oficina
- Sincronização em tempo real: qualquer instrutor lança e todos veem instantaneamente
- Dashboard analítica: rankings por saldo, positivos, negativos, grupamento e subunidade
- Filtro por semana: visualização separada para 1ª e 2ª semana de instrução
- Destaques diários: cálculo automático dos destaques diurno e noturno com seletor de dia
- Fora de Oficina: registro de FOs em momentos fora da instrução formal
- Histórico completo: todos os FOs com opção de exclusão
- Exportação de backup: download dos dados em JSON
- PWA: funciona como app ao ser adicionado à tela inicial no mobile

## Stack

| Camada | Tecnologia | Função |
| --- | --- | --- |
| Front-end | HTML5 | Estrutura da interface |
| Front-end | CSS3 | Estilização e layout responsivo |
| Front-end | JavaScript (ES Modules) | Lógica e manipulação de DOM |
| Back-end | Firebase Firestore | Banco NoSQL em tempo real na nuvem |
| Hospedagem | GitHub Pages | Serve o arquivo HTML publicamente |

## Arquitetura

```
┌─────────────────────────────┐
│       DISPOSITIVO DO USUÁRIO │
│                             │
│  index.html                 │
│  ├── HTML  → estrutura      │
│  ├── CSS   → aparência      │
│  └── JS    → lógica         │
│       └── Firebase SDK      │
└──────────────┬──────────────┘
               │ WebSocket
               │
┌──────────────▼──────────────┐
│   FIREBASE FIRESTORE        │
│   (servidores do Google)    │
│                             │
│   coleção: "fos"            │
│     └── documentos JSON     │
└──────────────┬──────────────┘
               │ onSnapshot()
               │ (notifica todos)
               │
┌──────────────▼──────────────┐
│   OUTROS DISPOSITIVOS       │
│   (outros instrutores)      │
│   tela atualiza em <1s      │
└─────────────────────────────┘
```

O app é um único arquivo HTML. O GitHub Pages serve esse arquivo quando alguém acessa a URL. A partir daí, o navegador roda o JavaScript que se conecta direto ao Firebase. O GitHub Pages sai do circuito.

## Configuração

Pule esta seção se quiser apenas testar a demo. Siga se quiser rodar uma instância privada sua.

### 1. Firebase

1. Acesse `console.firebase.google.com`
2. Crie um projeto
3. Vá em Firestore Database, Criar banco de dados, Modo de teste
4. Selecione a região `southamerica-east1`
5. Vá em Configurações do projeto, `</>` (Web)
6. Registre um app Web e copie o objeto `firebaseConfig`

### 2. Configure o arquivo

Abra o `index.html` e substitua o bloco de credenciais:

```javascript
const firebaseConfig = {
  apiKey: "SUA_API_KEY",
  authDomain: "SEU_PROJETO.firebaseapp.com",
  projectId: "SEU_PROJETO",
  storageBucket: "SEU_PROJETO.firebasestorage.app",
  messagingSenderId: "SEU_MESSAGING_SENDER_ID",
  appId: "SEU_APP_ID",
};
```

### 3. Personalize

No `index.html`, localize e ajuste:

**Grupamentos por semana:**

```javascript
const SEMANA_GPT = {
  "1ª Semana": ["ALFA", "BRAVO", "CHARLIE", "DELTA"],
  "2ª Semana": ["ECHO", "FOXTROT", "GOLF", "HOTEL"],
};
```

**Subunidades por grupamento:**

```javascript
const GPT_SU = {
  ALFA: ["1ª Cia", "2ª Cia", "Cia Eng"],
  // ...
};
```

**Instrutores por oficina:**

```javascript
const INSTRUTORES = {
  "Orientação Diurna": { "1ª Semana": "Ten Lima", "2ª Semana": "Ten Rocha" },
  // ...
};
```

### 4. Deploy

**Opção A: GitHub Pages (usada por este repo)**

1. Envie o repositório para o GitHub
2. Vá em Settings, Pages
3. Source: `main` / root
4. URL gerada: `https://seuusuario.github.io/nome-do-repo`

**Opção B: Netlify Drop (deploy pontual mais rápido)**

1. Acesse `drop.netlify.com`
2. Arraste o arquivo `index.html`
3. Compartilhe a URL gerada

## Estrutura do Código

```
index.html
├── <head>          : metadados, CSS embutido
├── <body>
│   ├── #loading    : tela inicial de carregamento
│   ├── #app
│   │   ├── header  : badge, estatísticas, indicador de sync
│   │   ├── nav     : 3 abas: Lançar, Dashboard, Histórico
│   │   └── main
│   │       ├── #view-lancar     : formulário de lançamento
│   │       ├── #view-dashboard  : destaques + rankings
│   │       └── #view-historico  : lista de FOs
│   └── <script type="module">
│       ├── Init do Firebase
│       ├── Estado global (state, fos, etc.)
│       ├── onSnapshot()     : listener em tempo real
│       ├── Navegação        : setView, setDashTab
│       ├── Formulário       : setSemana, setOficina, registrar
│       ├── Agregações       : rankSoldados, countByKey, destaqueDoDia
│       ├── Render           : renderDestaques, renderDash, renderHistorico
│       └── Utils            : getDateBRT, showFlash, exportJSON
```

## Conceitos-chave

**Firebase Firestore: por que NoSQL?** FOs são documentos autocontidos, sem relacionamentos complexos entre entidades que justifiquem um banco relacional. O Firestore oferece sincronização em tempo real nativa via WebSocket, eliminando a necessidade de polling.

**onSnapshot: listener em tempo real.** Em vez de consultar o banco periodicamente, o `onSnapshot` mantém uma conexão WebSocket aberta. O servidor notifica automaticamente todos os clientes conectados sempre que qualquer dado muda.

**Single Page Application sem framework.** A navegação entre telas é feita escondendo e mostrando divs com `display:none/block`. Todo o HTML de tabelas e cards é gerado dinamicamente via `innerHTML` com template literals. Sem React, Vue ou qualquer framework. JavaScript puro.

**Fuso BRT.** Timestamps são armazenados em UTC. A conversão para BRT (UTC-3) é feita subtraindo 3 horas em milissegundos antes de extrair a data, garantindo que FOs registrados após a meia-noite UTC caiam no dia correto do horário de Brasília.

## Uso como PWA

**iPhone/iPad:**

1. Abra a URL no Safari
2. Toque em Compartilhar, Adicionar à Tela de Início
3. O app abre em tela cheia sem a barra do Safari

**Android:**

1. Abra a URL no Chrome
2. Toque nos três pontos, Adicionar à tela inicial

## Licença

MIT. Livre para usar, modificar e distribuir.
