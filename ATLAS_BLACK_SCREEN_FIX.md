# ATLAS - correção da tela preta / "não abre"

## Sintoma
Depois de instalar um novo pack de atualização, o Atlas não abria: tela
preta, sem nenhum erro visível. Nem a tela vermelha de erro (CrashReportActivity)
nem o Logcat do CodeAssist mostravam nada.

## Causa mais provável
O app estava sendo empacotado com `debuggable false` + `minifyEnabled true`
(build "release") no `app/build.gradle`. Duas coisas quebravam ao mesmo tempo:

1. **`debuggable false` esconde o Logcat.** O Android só deixa uma ferramenta
   como o CodeAssist ler o log/anexar ao processo de outro app se esse app for
   "debuggable" (ou o aparelho for rooteado). Por isso o Logcat não mostrava
   absolutamente nada, mesmo rodando pelo botão Play - não era falha do
   CodeAssist, era o Android bloqueando o acesso.
2. **`minifyEnabled true` (R8) num projeto grande, buildado por uma ferramenta
   própria (o CodeAssist não usa o Gradle/Android Studio completo) é um ponto
   clássico de falha:** se a regra automática que mantém toda Activity/Service/
   Receiver citada no `AndroidManifest.xml` com o nome original não for gerada
   corretamente, o R8 pode renomear ou remover uma classe que o manifesto ainda
   aponta pelo nome antigo. O app crasha na hora de abrir a primeira tela, em
   toda abertura, antes de qualquer código próprio do Atlas (inclusive a tela
   de erro) rodar - exatamente o que estava acontecendo.

## O que foi mudado
- `app/build.gradle`: build `release` agora com `debuggable true`,
  `minifyEnabled false`, `shrinkResources false` (temporário, só pra depurar).
- `CrashReporter.java`: a tela vermelha de erro agora abre em qualquer tipo de
  build, não só em build "debuggable" (antes disso, um crash em build release
  era silenciado de propósito).
- `AtlasApplication.java`: a inicialização de som, varredura de apps
  instalados e da sessão de voz/IA agora roda cada uma isolada - uma falha
  numa não derruba as outras.

## Quando voltar ao normal
Depois que o Atlas estiver abrindo bem de novo, dá pra voltar
`minifyEnabled`/`shrinkResources` para `true` e `debuggable` para `false` no
`app/build.gradle` para o build final "de verdade" (arquivo menor, código
ofuscado). Só testar com calma depois de reativar, já que foi exatamente essa
combinação que provavelmente causou o problema.
