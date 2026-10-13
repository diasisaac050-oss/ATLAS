# ATLAS VOZ FIXA - mesma voz em todos os celulares

Alterado:
- voice/TTSManager.java

O que muda:
- O Atlas passa a usar SEMPRE o motor "Servicos de fala do Google" (com.google.android.tts),
  em vez do motor padrao de cada marca (Samsung, Xiaomi, Motorola...). Gratuito e offline.
- Depois de escolher o idioma, o Atlas escolhe UMA voz especifica:
  1) nome da lista preferida (pt-BR: pt-br-x-ptd-local, pt-br-x-afs-local, ...)
  2) vozes offline antes das online
  3) maior qualidade
  4) ordem alfabetica (desempate igual em todo celular)
- Se o Google TTS nao estiver instalado ou nao abrir, cai para o motor padrao do celular (nao fica mudo).
- A voz escolhida aparece no Logcat com a tag "AtlasTTS" ("Fixed voice: ...").

Observacoes:
- Velocidade e tom (Configuracoes) continuam funcionando.
- Para ficar 100% igual, o celular precisa ter a voz baixada em
  Configuracoes > Sistema > Idiomas > Saida de texto para fala > Google > Instalar dados de voz.
