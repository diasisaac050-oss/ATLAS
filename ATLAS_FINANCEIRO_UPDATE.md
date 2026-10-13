# ATLAS FINANCEIRO

Acesso: Configuracoes > Despesas. Tambem por Chat/Voz ("Atlas, marque uma despesa para mim").

## Arquitetura (integrada, sem segunda arquitetura)
- Banco: mesmo atlas.db, versao 5 (migracao so ADITIVA, tabelas `fin_*`; nada existente foi tocado).
  fin_categories, fin_series (mensal/parcelamento), fin_expenses (1 linha por ocorrencia), fin_payments.
  Indice unico (series_id, seq) => recorrencia/parcelas NUNCA duplicam.
- Dinheiro em centavos (long). Datas como yyyymmdd (classe Ymd, funciona na API 24).
- Status (PENDENTE/PAGA/PARCIAL/ATRASADA/CANCELADA) e sempre DERIVADO de valores+datas, nunca guardado.
- Valor original nunca e reescrito por pagamentos: so `paid_cents` (soma da tabela de pagamentos).
- Configuracoes financeiras em SharedPreferences proprio (`atlas_finance_prefs`), separado da memoria geral.
- Tudo offline. Nenhum dado sai do aparelho.

## Seguranca
- Senha de 6 digitos: so hash PBKDF2 (PasswordHasher existente) + criptografia Keystore (SecureStorage). Nunca em texto puro.
- Biometria/facial: `android.hardware.biometrics.BiometricPrompt` nativo (API 28+). Atlas nunca ve dados biometricos.
  Se a biometria sumir, usa o bloqueio de tela do celular (sem trancar o usuario fora).
- Atraso crescente apos 5 senhas erradas. FLAG_SECURE (sem print / tela em branco em "recentes").
- Desbloqueio so em memoria; trava ao sair da area (8 s de tolerancia).
- Registrar despesa por Chat/Voz NAO exige senha. Opcional: exigir desbloqueio para CONSULTAS por Chat/Voz.
- Notificacoes com "ocultar valores": versao publica neutra na tela bloqueada.

## Chat e Voz
`commands/FinanceCommandHandler` -> `finance/FinanceAssistant` (sem classes Android, testado). Roda ANTES dos outros
handlers para que respostas ("dia 10", "duzentos reais") caiam no fluxo certo; ignora frases de lembrete/agenda/alarme.

## Arquivos alterados fora de `finance/`
AndroidManifest.xml, AtlasDatabase.java (v5), CommandManager.java (1 hook), SettingsActivity.java + activity_settings.xml
(linha Despesas), BootReceiver.java (reagendar), strings (settings_despesas), 7 icones ic_fin_*.xml.
Nenhuma dependencia nova.
