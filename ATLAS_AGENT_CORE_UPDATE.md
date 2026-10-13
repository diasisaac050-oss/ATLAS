# ATLAS AGENT CORE - autonomia, personalidade, voz e paineis

Projeto modificado (nao e outro app). Modelo de IA (Groq gpt-oss-120b), memoria, historico e
todos os comandos locais continuam como estavam.

## O que mudou

### 1. Agente com ferramentas reais (pacote `agent/`)
- `AgentRunner`: laco entender -> agir -> verificar -> recuperar. O modelo escolhe ferramentas
  (function calling da Groq), o app executa por um registro fixo e devolve o resultado verificado.
  Nenhum codigo escrito pela IA e executado.
- Limites no codigo: 5 chamadas ao modelo, 8 de ferramenta, 70 s, chamada identica nunca repete,
  limite de 20 req/min compartilhado com o resto do app.
- Seguranca no codigo (nao so no prompt):
  - destrutivo/dinheiro (`SensitiveActionGuard`) so roda depois do "sim";
  - mensagem de WhatsApp nunca e enviada pelo agente: o chat abre com o texto pronto e voce toca em enviar;
  - depois de ler conteudo externo (web, noticias, mapas) toda ferramenta que muda algo e BLOQUEADA
    na mesma rodada (defesa contra texto malicioso em paginas);
  - telefones nunca vao para o modelo; so o nome falavel e o rotulo.
- Status honestos por ferramenta: ok / reported / partial / needs_info / needs_confirmation / blocked / failed.
  "ok" em abrir app significa "o Android aceitou o intent", nao "vi a tela".
- Ferramentas: `run_device_command` (reaproveita TODOS os comandos locais), `open_app`, `find_contact`,
  `whatsapp_open_chat`, `whatsapp_prepare_message`, `open_email_inbox`, `get_weather`, `get_news`,
  `web_search`, `find_places`, `open_url`.
- WhatsApp: abre a conversa pelo cartao do contato do proprio WhatsApp (prova que o contato tem WhatsApp),
  com plano B no link wa.me. "abre o whatsapp na conversa da minha princesa" agora chega ao agente
  (antes caia em "app nao encontrado").

### 2. Personalidade e inteligencia emocional
- Prompt reescrito: amigo espontaneo, informal, com humor que sabe quando NAO usar.
- `ConversationIntelligence.detectTone`: VENT / SERIOUS / HELP_REQUEST / CASUAL / NEUTRAL.
  Desabafo e assunto serio nao recebem ferramentas nem listas: resposta curta e natural
  ("preciso tirar 8,5 em matematica" -> pergunta se quer dicas). Pedido explicito de ajuda libera resposta completa.
  Pedido de acao no meio do desabafo ("meu dia foi ruim, abre o whatsapp") continua sendo executado.
- Saudacao ao abrir (`ProactiveGreeter`): o proprio modelo decide entre uma frase curta ou `[SILENCIO]`,
  vendo hora do dia, tempo desde a ultima conversa, memorias e suas ultimas saudacoes (que nao pode repetir).
  Sem frase fixa: se a IA falhar, fica em silencio. Limites: 6 min entre tentativas, 5 por dia.
  Aparece no Chat (texto) e no modo Voz (falada).

### 3. Voz
- TTS: fila por "geracao" e frases curtas; parar corta na hora e callbacks velhos sao ignorados; emojis
  nunca sao lidos.
- Escuta durante a fala: o reconhecedor continua ligado enquanto o Atlas fala, mas o que ele ouve vai
  SOMENTE para o `StopCommandDetector` e nunca para a IA. So interrompe com ordem de parada curta:
  "Atlas, para", "para de falar", "pare" ... Conversa paralela, TV, eco da propria voz e frases longas
  que contem "para" sao ignoradas. "cala a boca/silencio/chega" so valem dirigidos ao Atlas.
  Ordens ambiguas ("para" sozinho) so valem se continuarem iguais por 450 ms ou no resultado final.
- Botao de parar e toque continuam cortando na hora.
- Configuracoes > chave "Continuar ouvindo enquanto o ATLAS fala" (ligada por padrao) para desligar
  se o seu aparelho se comportar mal.

### 4. Nomes de contatos
`SpeechText.contactName`: "Minha Princesa [fita][coracao]" e falado "Minha Princesa". So emoji -> "um contato
sem nome legivel". Letras de qualquer alfabeto sao preservadas. O nome salvo, o id e a busca continuam
intactos (a busca ate ficou mais tolerante a enfeites).

### 5. Paineis
`PanelActivity` (Views nativas, sem imagem gerada) para clima (Open-Meteo), noticias (Google News RSS),
pesquisa e lugares (OpenStreetMap). Toque numa linha abre o link/mapa.

## Configuracao
- Obrigatorio: sua chave Groq em `ApiKeyConfig` (como ja era).
- Opcional: `BRAVE_SEARCH_API_KEY` em `ApiKeyConfig` (https://brave.com/search/api/, plano gratis)
  para busca na web de verdade (produtos, precos). Sem ela, a pesquisa usa so Wikipedia/DuckDuckGo
  (bom para fatos; ruim para compras) e o Atlas avisa a fonte.
- Cidade: o app nao tem permissao de localizacao. Clima/lugares usam a cidade dita ou uma memoria
  chamada "cidade". Diga "lembra que moro em Santos" uma vez.

## O que foi testado de verdade (JVM, neste ambiente)
Sem Android SDK e sem internet aqui, entao NAO foi gerado APK nem testado em aparelho.
- 84 testes unitarios passando: pronuncia de contatos (emoji, bandeira, ZWJ, japones, hindi...),
  deteccao de parada vs conversa paralela vs eco, tom emocional (inclui seus exemplos), guarda de acoes
  sensiveis, painel, e o laco do agente com modelo falso (rota alternativa apos falha, chamada duplicada,
  bloqueio apos conteudo externo, confirmacao, limite de passos, falha do modelo no meio, ferramenta que quebra).
- 25 testes de parsing: resposta do Open-Meteo (pt/en), RSS do Google News (incl. ataque XXE bloqueado),
  Brave, resposta de tool-calling da Groq.
- Todo o codigo do projeto passou pelo parser/analisador do javac sem erro de sintaxe; os unicos erros
  restantes sao classes do Android ausentes neste ambiente.

## NAO testado / limites reais
- Compilacao Gradle/CodeAssist e execucao em celular: nao feitas. Se o CodeAssist apontar erro, mande o log.
- Chamadas de rede reais (Groq com tools, Open-Meteo, Google News, Nominatim, Brave): formato conferido
  com respostas simuladas, nao com a rede.
- Escuta durante a fala depende do aparelho: alguns tocam o "bip" do reconhecedor a cada reinicio, outros
  abaixam/pausam o audio ao ativar o microfone, e o eco no alto-falante pode atrapalhar. Se o reconhecedor
  falhar 4 vezes seguidas durante uma resposta, o app desiste dela (o botao parar segue funcionando).
- E-mail: NAO le mensagens. Ler Gmail exige login OAuth e cliente registrado, que o app nao tem; a
  ferramenta so abre a caixa de entrada e diz isso.
- WhatsApp: o app nao enxerga a tela do WhatsApp (nao usa Acessibilidade de proposito), entao nao da para
  confirmar qual conversa ficou visivel nem enviar sozinho; so prepara e voce toca em enviar.
- Lugares: Nominatim e geocodificador, nao diretorio de comercios (sem horario, nota ou distancia).
- Plano gratis da Groq tem limite de tokens por minuto; varias etapas seguidas podem tomar 429 (o app
  responde com o erro padrao, ou com o que ja conseguiu fazer, se algo ja tinha sido feito).
- Textos novos existem em en, pt-BR e pt-PT; nos outros idiomas caem para ingles. Descricao do clima
  so em pt/en.
- A janela de confirmacao falada ("sim") vale 45 s.

## v1.4.0 - limpeza (pedido posterior)
- Removidos: bloco de notas (notas, projetos, checklists, lixeira, cofre e os comandos de voz de notas),
  tela de Fontes (o app usa a fonte padrao do sistema) e todo o ecossistema de Despesas (telas, comandos,
  notificacao diaria, agendamento no boot, permissao de PIN/biometria so desse modulo).
- Botoes/linhas de notas, fontes e despesas saem de Configuracoes, Chat e Voz; manifest e textos de ajuda ajustados.
- Dados antigos: as tabelas de notas e despesas continuam dentro do banco do aparelho (nao foi feita migracao
  destrutiva); ficam inacessiveis e nao sao usadas. Um "limpar dados" do app as apaga.
- "lembra que ..." continua salvando na Memoria; "criar nota" agora cai na conversa normal.
- Novidades reescritas (en, pt-BR, pt-PT), pacote "ATLAS AGENT CORE", versao 1.4.0 (versionCode 7).
- Verificacao: sem referencias Java ou de recursos quebradas (varredura de simbolos/layout/drawable/id/string) e
  XMLs validos; testes de logica seguem 84/84. Ainda sem compilar no Gradle.
