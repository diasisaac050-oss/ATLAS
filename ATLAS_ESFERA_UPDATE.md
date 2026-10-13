# ATLAS ESFERA - a Home agora e uma esfera 3D viva (v1.5.0, versionCode 8)

Novos (pacote sphere/):
- SphereEngine.java: motor 3D em Java puro (sem Android). Camadas independentes:
  malha esferica (~5k pontos em aneis, aparecendo em manchas), ~120 nos com
  ligacoes que surgem/somem e pulsos viajando, nucleo de ~1500 pontos com glow,
  200 particulas em orbita (3 cinturoes + soltas), particulas que saem da
  superficie e voltam, ondas de energia. Perspectiva real, parallax por camada.
- SpherePainter.java: interface de desenho (pontos, linhas, glow).
- AtlasSphereView.java: View do Android (loop de quadros, toque, acelerometro,
  desenho aditivo). Transparente: os fundos animados dos temas continuam visiveis.
  Qualidade adaptativa: se o quadro demorar demais, cai para o nivel leve.

Alterados:
- activity_main.xml: Home limpa (marca ATLAS, engrenagem, esfera, estado, texto,
  barra inferior com historico, chat, microfone, parar, modos).
- MainActivity: usa a esfera; icones pegam a cor do tema; botao Parar so aparece
  enquanto o ATLAS fala; novo botao Modos; toque na esfera = onda de energia,
  arrastar = girar com inercia (os easter eggs de toques rapidos/segurar continuam).
- HudStateManager: novo construtor (esfera, rotulo); os estados viram esfera + texto.
- AtlasVoiceSession: segundo ouvinte de nivel de audio (o da Presenca nao muda).
- strings (todos os idiomas): state_ready ("ATLAS ESTA PRONTO"); Novidades e nome
  do pacote trocados (en, pt-BR, pt-PT). build.gradle: versao 8 / 1.5.0.

Comportamento:
- Estados: espera, ouvindo, processando/pensando, executando, respondendo, erro
  (esfera vermelha), offline (apagada). A transicao e suave.
- Voz: ouvindo usa o nivel real do microfone; respondendo usa um envelope de fala
  sintetico (o Android nao entrega o volume do TTS sem permissao extra).
- Temas: toda cor sai da cor de destaque do tema; geometria e animacao sao iguais.
- O modo Chat continua com o orb antigo (ChatOrbView).

Correcao: "Consegui fazer uma parte, mas nao terminei: Real results from..."
- AgentRunner: se a chamada ao modelo falhar DEPOIS de uma ferramenta (limite da Groq,
  timeout, rede, erro 5xx), tenta de novo uma vez apos uma pausa curta.
- Se ainda assim falhar e ja houver painel na tela, mostra "Abri os resultados na tela,
  mas nao consegui resumir agora" em vez da nota interna em ingles da ferramenta.
- SearchTool: manda menos texto de volta ao modelo (4 resultados, trechos de 120).
- AIManager: limite proprio de pedidos/min 20 -> 30 (cada pergunta do agente usa 2+).
- strings: agent_partial_panel (en, pt-BR, pt-PT). A esfera nao foi alterada.

Modo Presenca com a esfera (mesmo pacote ATLAS ESFERA)
- Os 4 layouts (Vidro, Bola, Nucleo, Card) agora desenham a esfera 3D, cada um no seu
  lugar e com a sua entrada: Vidro cai do topo, Bola sobe de baixo com overshoot,
  Nucleo sobe grande ate o meio, Card floresce no centro. Tamanhos das janelas e
  legendas nao mudaram.
- Novos: presence/PresenceSphereDrawer, PresenceSpherePlacement, sphere/SphereCanvasPainter
  (o desenho antes embutido na AtlasSphereView, agora compartilhado).
- Cor do tema (antes era azul fixo), estado de voz (ouvindo, processando, falando, erro),
  nivel do microfone e onda de energia no toque (ripple).
- Fundo escuro suave atras da esfera para ela ler bem sobre apps claros.
- Desempenho: motor com 3 niveis de qualidade (0 completo, 1 leve, 2 minimo). A Presenca
  comeca no 1, as previas em Configuracoes usam o 2, e todas descem sozinhas se o quadro
  demorar. A esfera da Home tambem passa a poder descer ate o 2; no nivel 0 nada mudou.
- Os painters antigos (vidro azul) ficaram no projeto, sem uso.
- Novos nomes dos layouts: Gota (Vidro), Emergir (Bola), Ascensao (Nucleo), Bloom (Cartao), com descricoes reescritas (en, pt-BR, pt-PT).

Abertura "Atlas acordando" (v1.5.4)
- SplashActivity refeita: tela preta, o nucleo cresce e acende em branco (AtlasSphereView.startAwakening),
  ao terminar troca para a cor do tema e o fundo vai de preto ao fundo do tema; entao abre a Home sem
  transicao e sem salto (mesma posicao da esfera) e so os botoes aparecem (fade).
- Removido tudo da splash antiga (aneis, logo, barra, textos). Tema da splash agora preto, sem fullscreen
  (para a esfera ficar na mesma posicao da Home) e sem animacao de janela.
- BaseActivity.shouldApplyThemeBackground() (a splash mantem o preto ate o fim da expansao).
- Estimativa da area da Home na splash: topo 66dp, base 198dp (HOME_TOP_DP/HOME_BOTTOM_DP em SplashActivity) - ajustar se a esfera "pular" na transicao.

Temas (v1.5.5)
- Removidos os grupos Pulso, Regal e Vibrante: 11 temas (Solar Flare, Crimson Pulse, Amber Circuit, Solar Ember, Void Pulse,
  King Green, Jade Empire, Golden Serpent, Bumblebee, Honeycomb, Sakura Storm) + 3 classes de fundo + strings. Sobram 39 temas.
  Quem tinha um deles selecionado cai no Normal (getPaletteById ja faz isso).
- Novo theme/FxBackgroundView: base com luz aditiva (halo + centro quase branco), fundo com luz suave, animacao por tempo (dt),
  pausa quando a tela esta oculta, sem alocacao por quadro.
- Particulas: particulas subindo de um chao luminoso, 3 camadas de profundidade, balanco e brilho.
- Enxame: bando orbitando dois centros que vagam, com linhas entre agentes proximos (a rede de nos da esfera).
- Brilho: feixes de luz diagonais que passam e descansam + brilhos em cruz (glints).
- Glacial: luz palida sob o gelo, cristais de geada caindo devagar (alguns piscam) e facetas com reflexo viajando.
- Chrome Pulse mantem o nome (e do grupo Brilho).

Correcao: Presenca desligava apos atualizar o app (v1.5.6)
- Causa: ao atualizar, o Android encerra o servico da Presenca; so o BOOT_COMPLETED o religava, entao a opcao continuava "ligada" mas nada rodava.
- BootReceiver agora tambem ouve MY_PACKAGE_REPLACED (religa Presenca e realarma lembretes/alarmes, que o update tambem limpa).
- AtlasApplication.onCreate chama PresenceManager.restartIfEnabled (rede de seguranca ao abrir o app).

Presenca: animacoes de entrada/saida + esfera "estacionada" na lateral
- PresenceSpherePlacement: a esfera nasce pequena (5% do tamanho) e cresce enquanto chega; na saida a mesma curva roda ao contrario (contrai e volta por onde veio). Vidro: cai do topo; Bola: sobe de baixo; Nucleo: sobe pequeno e so incha perto do meio (na saida contrai primeiro e depois afunda); Card: floresce de um ponto no centro. Opacidade agora sobe suave (antes aparecia quase de uma vez).
- PresenceBubbleView: aberturas um pouco mais longas e saidas bem mais longas (ease-in-out) para a contracao ser vista.
- PresenceOverlayController: o fallback que removia a janela passou de 700 ms para 1100 ms (cortava a saida do Nucleo).
- Arrastar a esfera para o lado (qualquer layout): ela encolhe e a janela vira um quadrado pequeno (96dp) colado na borda; o resto da tela fica tocavel e a conversa por voz continua. Arrastar a esfera estacionada reposiciona (gruda na borda mais proxima, qualquer altura). Duplo toque nela = volta ao normal (com a entrada); segurar = fecha; toque simples continua igual. Sem a legenda enquanto estacionada.
- Arquivos: PresenceTouchHandler (arrasto lateral), PresenceBubbleState (dock/docked), PresenceCaption (setSuppressed), PresenceSphereDrawer (publica o centro da esfera).
