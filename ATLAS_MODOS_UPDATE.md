# ATLAS MODOS

Nova aba em Configuracoes > Modos, com dois modos de uso:

- VOZ (padrao, o que ja existia): microfone sempre ligado estilo ligacao, o ATLAS responde falando, Presenca (chacoalhar) ativa.
- CHAT (novo): so por escrito. Sem microfone, sem fala e sem Presenca. Lembretes, alarmes, calendario, notas, ligacoes, abrir app e IA funcionam digitando (mesmo CommandManager/AIManager do modo Voz).

## Como funciona
- ModeManager (utils): guarda o modo e diz qual tela e a "home" (homeIntent).
- ModesActivity: dois cards com preview animado (onda de voz / mini conversa). Toque escolhe, botao inferior aplica e reabre o ATLAS no modo novo (CLEAR_TASK).
- ChatActivity + pacote chat/: tela do Chat (bolhas que entram com mola, resposta que "se escreve", pontinhos pulando, orb do ATLAS no topo, sugestoes na tela vazia, botao de enviar que voa, copiar com toque longo, confirmacao Sim/Nao dentro da bolha, tocar no erro tenta de novo). Segue a cor de destaque do tema.
- ChatSession: cerebro do chat (vive no processo, a conversa nao se perde ao ir em Configuracoes). Salva no Historico como "Chat <numero>" e restaura ao reabrir. Botao "+" no topo inicia nova conversa.
- BaseActivity so liga o microfone em modo Voz; AtlasVoiceSession.suspendForChatMode() desliga tudo ao entrar no Chat.
- PresenceManager.syncWithMode: o servico da Presenca so roda em modo Voz (a escolha do usuario nao e apagada).
- MainActivity redireciona para o Chat quando o modo e Chat (notificacoes, Presenca e pedidos de permissao continuam funcionando).
- Splash, Onboarding e Idioma abrem a home do modo atual.

## Arquivos novos
activities/ChatActivity, activities/ModesActivity, chat/* (ChatAdapter, ChatMessage, ChatOrbView, ChatSession, ChatStyle, ModePreviewView, TypingDotsView), utils/ModeManager, layouts activity_chat, activity_modes, item_chat_atlas/user/typing, drawables ic_chat, ic_modes.

## Strings
Novas em values (ingles), values-pt-rBR e values-pt-rPT, sem acento (regra do Code Assist).
