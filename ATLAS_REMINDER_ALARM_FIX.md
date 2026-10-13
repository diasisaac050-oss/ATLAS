# ATLAS - Lembretes e despertador em qualquer jeito de falar

## O que mudou
- Despertador com hora exata: "meio-dia e 39", "12:39", "as 7 e meia", "7h30", "as 6 da manha",
  "quinze para as 7", "meia-noite", "amanha as 6", "sexta as 5 da manha", "dia 25 as 8".
- Despertador relativo ampliado: "daqui a 5 minutos", "em cinco minutos", "daqui a uma hora e meia",
  "meia hora", "de 10 minutos". Numeros por extenso funcionam ("vinte e cinco").
- Lembretes: qualquer frase com acao (marque, coloca, agenda, anota, me lembra, cria...) + dia ou hora.
  Dia: "dia 20 de abril", "20/04", "amanha", "sexta", "segunda que vem", "dia 20", "daqui a 3 dias".
- Titulo: o que sobra da frase depois de tirar comando e data.
  "marque no dia 20 de abril como dia do presente da minha mae" -> "Dia do presente da minha mae".
- Se faltar algo, o ATLAS pergunta so aquilo ("Para que horas?", "Para quando?", "Como eu chamo esse
  lembrete?") e entende a proxima frase como resposta. "Deixa pra la" cancela.
- Acabou o "fale assim": mensagem de erro agora nao ensina frase.

## Arquivos
Novos (calendar): PtNumberWords, DateExpressionParser, ClockExpressionParser,
DurationExpressionParser, TimeResolver, TitleExtractor
Novos (commands): ReminderIntentDetector, ReminderAlarmHandler
Alterados: DateTimeCommandParser (reescrito), CommandParser, CommandManager,
values/strings.xml, values-pt-rBR/strings.xml, values-pt-rPT/strings.xml
