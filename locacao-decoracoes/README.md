# Locação de Decorações — Kits Pegue e Monte

Catálogo/site para locação de kits de decoração de festas no formato
**pegue e monte**: o cliente escolhe o tema, retira o kit, monta em casa
seguindo um guia e devolve depois do evento.

Este projeto é **totalmente independente** do catálogo Natunutri — vive
nesta pasta apenas até você criar o repositório novo no GitHub. Depois é
só copiar o conteúdo desta pasta para a raiz do repositório novo.

## O que já vem pronto

- `index.html` — site completo em um arquivo só:
  - Catálogo de kits com fotos, categoria, itens inclusos, preço de
    locação e valor de caução.
  - Fluxo de reserva: cliente escolhe data de retirada/devolução, o site
    verifica se o kit está livre nesse período e finaliza a reserva pelo
    WhatsApp.
  - Painel administrativo (protegido por login) para cadastrar kits,
    confirmar/cancelar reservas e editar nome da loja e WhatsApp.
  - Funciona **sem nenhuma configuração** com dados de demonstração
    (4 kits fictícios) — dá pra testar tudo localmente antes de ligar a
    nuvem.

## Como testar agora, sem configurar nada

Abra o arquivo `index.html` direto no navegador (duplo clique) ou publique
a pasta em qualquer hospedagem estática (GitHub Pages, Netlify, Vercel).
O site funciona com os 4 kits de exemplo e o botão de admin (⚙️ no canto
inferior direito) avisa que a nuvem ainda não está conectada.

## Como ligar a nuvem (Firebase) — sincroniza kits e reservas entre aparelhos

1. Crie um projeto novo em https://console.firebase.google.com (separado
   do projeto do Natunutri).
2. Ative **Firestore Database** (modo produção) e **Authentication** →
   método **E-mail/senha**.
3. Em Authentication → Users, crie o usuário admin (seu e-mail e uma
   senha) — é o login usado no painel administrativo do site.
4. Em Configurações do projeto → Seus apps → Web, registre um app e copie
   o objeto `firebaseConfig`.
5. Cole esse objeto no `index.html`, dentro do último `<script
   type="module">`, substituindo os campos `"COLE_AQUI"`.
6. Configure as regras do Firestore (arquivo `firestore.rules` nesta
   pasta) em Firestore Database → Regras.
7. Pronto: o site passa a ler/gravar `kits`, `reservas` e `config/geral`
   na nuvem automaticamente.

### Regras de segurança (`firestore.rules`)

O site usa três coleções:

- `kits` — leitura pública, escrita só do admin autenticado.
- `reservas` — dados completos de cada reserva (nome/telefone do
  cliente). Qualquer visitante pode **criar** uma (fluxo público de
  reserva), mas só o admin autenticado pode **ler** a lista, confirmar ou
  cancelar. Isso evita expor nome/telefone de clientes a quem não tem
  login.
- `disponibilidade` — um espelho público e mínimo de cada reserva (só
  kit + datas + status, sem dados do cliente), usado só para o site saber
  se um kit está livre num período sem exigir login.

Copie o conteúdo de `firestore.rules` para a aba "Regras" no console do
Firestore.

**Limitação conhecida:** como `disponibilidade` aceita criação pública
(sem validação de conteúdo), alguém mal-intencionado poderia, em teoria,
gravar registros falsos para "bloquear" datas de um kit. Para o uso normal
do site isso não acontece, mas se isso virar um risco real (concorrência,
abuso), o próximo passo é validar as escritas com uma Cloud Function ou
ativar o App Check no projeto Firebase.

## Como migrar para o repositório novo do GitHub

1. Crie o repositório na sua conta nova.
2. Copie todos os arquivos desta pasta (`index.html`, `README.md`,
   `firestore.rules`) para a raiz do repositório novo.
3. Faça o primeiro commit e push.
4. (Opcional) Ative o GitHub Pages nas configurações do repositório para
   publicar o site direto do GitHub, sem custo.

## Personalização rápida

- **Nome da loja e WhatsApp**: pelo painel admin (aba "Configurações"),
  ou editando `configLoja` no início do `<script>` do `index.html`.
- **Kits/temas**: pelo painel admin (aba "Kits"), ou editando o array
  `kitsDemo` no `index.html` enquanto a nuvem não está ligada.
- **Cores**: variáveis CSS no topo do `index.html` (`:root { --roxo: ... }`).
