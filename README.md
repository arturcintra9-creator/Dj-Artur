# 🎧 DJ do Artur — Agente de Playlists no Spotify (n8n)

Um agente de IA no [n8n](https://n8n.io) que conversa por chat e cria **playlists personalizadas no Spotify** a partir de qualquer pedido de vibe, estilo, humor ou ocasião. Também controla o player: tocar, pausar, próxima faixa e tocar playlists já criadas.

> Diga algo como *"pop dos anos 2000 pra treinar"*, *"MPB tranquila pra trabalhar"* ou *"funk pra festa"* e ele monta a playlist na hora.

---

## O que ele faz

- 🧠 **Curadoria inteligente:** escolhe exatamente o nº de faixas pedido, sem repetir música/artista, com músicas reais e encontráveis no Spotify.
- 🌎 **Entende o idioma/região** do pedido (pede em português → prioriza artistas do cenário certo).
- 🎚️ **Ordena como um set de DJ:** abre forte, varia a energia e fecha bem.
- 📝 **Gera nome + descrição** automáticos para a playlist.
- ▶️ **Controla o player:** play / pause / próxima / tocar uma playlist existente.

---

## Como instalar (import)

### 1. Pré-requisitos
- Uma instância do **n8n** (cloud ou self-hosted).
- Uma conta **Spotify** (a playlist é criada na conta de quem configurar as credenciais).
- Uma chave da **OpenAI** — recomendado usar a sua própria (o crédito grátis embutido do n8n acaba rápido em uso público).

### 2. Importar o workflow
No n8n: **Workflows → ⋯ → Import from File** e selecione o `dj_artur.json`.

### 3. Conectar as credenciais
Abra os nós que mostram aviso de credencial e conecte as suas:
- **Spotify** (`spotifyOAuth2Api`) — em todos os nós Spotify e Spotify Tool.
- **OpenAI** (`openAiApi`) — nos nós *OpenAI Chat Model* e *OpenAI Chat Model2*.

> Dica Spotify: crie um app em https://developer.spotify.com/dashboard, pegue Client ID/Secret e use como Redirect URI a que o n8n mostrar no nó de credencial.

### 4. ⚠️ Religar o sub-workflow (passo essencial)
O nó **"Create new playlist"** chama um sub-workflow por ID. Depois de importar, esse ID muda, então:
1. Abra o nó **Create new playlist**.
2. No campo **Workflow**, selecione **este mesmo workflow** (o DJ do Artur) na lista.
3. Salve.

Sem isso, o chat funciona mas a criação de playlist falha.

### 5. Ativar e usar
- Ative o workflow (toggle **Active**).
- Abra o nó **When chat message received** → copie a **URL de chat pública** e comece a conversar.

---

## Publicar como template

Para outras pessoas usarem com as **contas delas**:
- **n8n Creator Hub:** envie o workflow em https://n8n.io/creators/ (as credenciais são removidas automaticamente na publicação).
- **GitHub:** basta compartilhar o `dj_artur.json` + este README. Quem importar segue os passos acima com as próprias contas.

> Este arquivo já vem **sem** dados da instância original (sem credenciais, IDs de instância ou estado ativo), pronto pra distribuir.

---

## Como funciona (arquitetura)

```
Chat  ─▶  Spotify Agent (LLM + memória)
                 ├─ tools de player: play / pause / próxima / tocar playlist / listar playlists
                 └─ tool "Create new playlist" ─▶ sub-workflow:
                        Ideate (LLM: escolhe faixas, nome, descrição)
                        └▶ Create playlist ─▶ Split faixas ─▶ Buscar cada faixa no Spotify
                                            └▶ Add faixas ─▶ Retorna link público
```

## Personalizar
- **Modelo de IA:** troque `gpt-4.1-mini` nos nós *OpenAI Chat Model* por um modelo mais forte para melhor conhecimento musical.
- **Estilo do DJ:** edite o *system message* do nó **Ideate playlist**.
- **Boas-vindas do chat:** edite `initialMessages` no nó **When chat message received**.
