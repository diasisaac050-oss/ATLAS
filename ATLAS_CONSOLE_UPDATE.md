# ATLAS CONSOLE - o Atlas obedece o painel (v1.3.0, versionCode 6)

Novos:
- control/RemoteControl.java: le /control (bloqueio, versao minima, aviso, IA)
- control/Telemetry.java: sinal de vida anonimo (a cada 3 h) + envio do ultimo erro
- activities/MaintenanceActivity.java: tela cheia, so o botao "Aguardar"

Alterados:
- BaseActivity: onResume chama RemoteControl.guard(); isMaintenanceExempt()
- AlarmRingActivity: isento (alarme nunca e coberto)
- AIManager.ask(): respeita o liga/desliga da IA (codigo de erro -104)
- AtlasApplication: inicia a telemetria
- AndroidManifest + strings (en, pt-rBR, pt-rPT) + versao 6 / 1.3.0

Comportamento:
- Sem internet / servidor fora = NAO bloqueia nada.
- Versao abaixo da minima = mesma tela "Aguardar" com a mensagem de atualizacao.
- Aviso aparece uma vez por usuario (por id de aviso).
