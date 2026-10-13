# ATLAS VISAO — envio de imagem + ditado por voz no modo Chat

> **Correção (26/09):** a primeira versão deste pacote usava o modelo
> `meta-llama/llama-4-scout-17b-16e-instruct`, que a Groq descontinuou em
> 17/07/2026 para contas free/developer — por isso o app retornava erro
> **404** ao analisar fotos. Trocado para `qwen/qwen3.6-27b`, o modelo de
> visão atual recomendado pela própria Groq (mesmo formato de requisição,
> nada mais mudou). Se você já tinha importado a versão anterior, é só
> importar este pacote por cima de novo.

Este pacote adiciona duas coisas ao modo Chat (tela de texto do ATLAS):

1. **Enviar uma imagem e o ATLAS analisa** (usando um modelo de IA com visao).
2. **Microfone de ditado**, igual ao do app do Claude: você toca, fala, e o
   texto aparece na caixa de digitação — o ATLAS continua respondendo só por
   escrito, não é um modo de voz contínuo.

Nada no modo Voz (chamada) foi alterado.

---

## 1. Arquivos deste pacote

Copie cada arquivo abaixo para o mesmo caminho dentro do seu projeto (todos
usam o pacote `com.idcorporation.atlas`). Os que já existem serão
substituídos; os novos são o `ImageUtils.java` e o `ic_image.xml`.

```
app/src/main/java/com/idcorporation/atlas/utils/ImageUtils.java      [NOVO]
app/src/main/java/com/idcorporation/atlas/ai/AIProvider.java         [editado]
app/src/main/java/com/idcorporation/atlas/ai/AIManager.java          [editado]
app/src/main/java/com/idcorporation/atlas/ai/GroqProvider.java       [editado]
app/src/main/java/com/idcorporation/atlas/chat/ChatMessage.java      [editado]
app/src/main/java/com/idcorporation/atlas/chat/ChatSession.java      [editado]
app/src/main/java/com/idcorporation/atlas/chat/ChatAdapter.java      [editado]
app/src/main/java/com/idcorporation/atlas/activities/ChatActivity.java [editado]
app/src/main/res/drawable/ic_image.xml                               [NOVO]
app/src/main/res/layout/activity_chat.xml                            [editado]
app/src/main/res/layout/item_chat_user.xml                           [editado]
app/src/main/res/values/strings.xml                                  [editado]
app/src/main/res/values-pt-rBR/strings.xml                           [editado]
app/src/main/res/values-pt-rPT/strings.xml                           [editado]
```

Nenhuma dependência nova foi adicionada ao `build.gradle` e nenhuma permissão
nova entrou no `AndroidManifest.xml` — o `RECORD_AUDIO` que o ditado usa já
existia (o modo Voz já pedia essa permissão). Escolher a imagem usa o
seletor de arquivos do próprio Android (`ACTION_OPEN_DOCUMENT`), que não
exige nenhuma permissão de armazenamento.

---

## 2. Como configurar a API para o ATLAS analisar fotos

**Boa notícia: se o chat de texto já está funcionando hoje, a análise de
imagem já vai funcionar também, sem nenhuma configuração extra.**

O motivo: o ATLAS já usa a Groq (`https://console.groq.com`) para o chat de
texto, com a chave em:

```
app/src/main/java/com/idcorporation/atlas/ai/ApiKeyConfig.java
```

```java
public static final String GROQ_API_KEY = "COLE_SUA_CHAVE_GROQ_AQUI";
```

A Groq também oferece um modelo com visão (`qwen/qwen3.6-27b`),
usando **a mesma chave e o mesmo endpoint** — só muda o nome do modelo na
requisição, o que o `GroqProvider.java` já faz sozinho quando uma imagem é
enviada. Não existe uma "segunda API" para configurar.

### Se você ainda não tem uma chave da Groq:

1. Acesse **https://console.groq.com/keys** e crie uma conta (não pede
   cartão de crédito).
2. Clique em **Create API Key**, dê um nome (ex: "ATLAS") e copie a chave
   (começa com `gsk_...`).
3. Cole essa chave dentro das aspas em `ApiKeyConfig.java`, substituindo
   `COLE_SUA_CHAVE_GROQ_AQUI`.
4. Recompile o app.

### Se você já tem uma chave configurada:

Não precisa fazer nada — é só compilar com os arquivos deste pacote e
testar (veja o passo a passo abaixo).

### Limites do plano gratuito da Groq (setembro/2026)

O plano free do modelo de visão (`qwen3.6-27b`) permite
cerca de **30 requisições por minuto e 1.000 por dia**, por organização
(ou seja, por chave). Isso é bem confortável para uso pessoal. Se o ATLAS
retornar erro 429 ("Limite de requisições da Groq atingido..."), é esse
limite sendo atingido — a mensagem de erro já trata esse caso e pede pra
tentar de novo em instantes.

---

## 3. Como testar

1. Compile e abra o modo Chat.
2. Toque no ícone de imagem 🖼️ (ao lado esquerdo da caixa de texto).
3. Escolha uma foto na galeria — uma miniatura aparece acima da caixa de
   texto, com uma barra de carregamento enquanto a imagem é processada
   (redimensionada e convertida) em segundo plano.
4. (Opcional) Digite uma pergunta sobre a imagem, tipo "o que tem nessa
   foto?" ou "transcreva esse texto".
5. Toque em enviar. A imagem aparece na sua bolha de mensagem, e o ATLAS
   responde descrevendo o que viu.
6. Para testar o ditado: com a caixa de texto vazia, o botão de enviar vira
   um microfone 🎤. Toque, fale, e o texto vai aparecendo em tempo real. Ao
   parar de falar, o texto fica pronto na caixa — toque em enviar
   normalmente (ou toque no microfone de novo pra parar de ouvir antes da
   hora).

---

## 4. Detalhes técnicos (se quiser entender o que mudou)

- **`ImageUtils.java`** — lê a imagem escolhida, corrige a rotação usando o
  EXIF (fotos tiradas com a câmera costumam vir "deitadas"), redimensiona
  para no máximo 1024px no lado maior e converte para JPEG em base64. Tudo
  roda fora da thread principal.
- **`GroqProvider.sendMessageWithImage(...)`** — monta a mesma requisição de
  sempre, mas troca o modelo para `qwen3.6-27b` e envia
  o conteúdo da última mensagem como um array `[texto, imagem]` (formato
  padrão OpenAI/Groq), com a imagem como `data:image/jpeg;base64,...`.
- **`ChatSession.sendUserMessageWithImage(...)`** — uma imagem anexada
  sempre vai direto para o modelo de visão (não passa pelos comandos de
  lembrete/alarme/etc., já que "olhar uma foto" não é um desses comandos).
  Se a imagem for enviada sem legenda, uma pergunta padrão ("O que você vê
  nessa imagem?") é usada.
- **Ditado (`ChatActivity` + `SpeechRecognizerManager`)** — reaproveita o
  mesmo reconhecedor de voz que o modo Voz já usa. Aqui ele só preenche a
  caixa de texto (com resultados parciais em tempo real); nada é enviado
  automaticamente e o ATLAS nunca fala em voz alta no modo Chat.
- **Limitação conhecida**: a imagem anexada não é salva no histórico de
  conversas (`HistoryManager` só guarda texto) e, se a análise falhar, o
  botão de "tentar de novo" não aparece nessa mensagem — é só anexar a foto
  de novo, pra evitar reenviar só o texto sem a imagem por engano.

---

Feito para o **ATLAS**, por **ID | CORPORATION**.
