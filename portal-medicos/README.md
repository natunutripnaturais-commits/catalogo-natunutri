# Portal do Prescritor — Botica Beladona

Área com login, exclusiva para médicos prescritores parceiros. Cada médico
tem sua própria conta (cadastrada por você) e vê uma biblioteca de
materiais — texto, PDF e vídeo — sobre novos produtos, modo de uso e
conteúdo institucional. Você (farmacêutico responsável) entra como
**admin**: cadastra/ativa/desativa médicos e publica os materiais.

Este projeto é **independente** do Firebase usado no `etiquetas.html` —
usa seu próprio projeto, para manter os dados clínicos/de médicos
separados do sistema de estoque.

## Arquivos

- `index.html` — o portal (login + área do médico + área do admin).
- `firestore.rules` — regras de segurança do Firestore.

## Como testar agora, sem configurar nada

Abra `index.html` direto no navegador. Sem o Firebase configurado, ele
entra sozinho em **modo demonstração** (faixa amarela no topo) com 2
médicos e 3 materiais de exemplo. Digite qualquer e-mail/senha na tela de
login pra entrar como admin — dentro da área admin aparece um botão
**"Ver como médico"** no topo pra alternar e ver a outra tela também.
Nada digitado no modo demo é salvo de verdade.

## Como ligar de verdade (Firebase)

### 1. Criar o projeto

1. Acesse https://console.firebase.google.com e crie um projeto novo
   (separado do projeto do Natunutri).
2. Ative **Firestore Database** (modo produção, escolha a região mais
   próxima do Brasil, ex. `southamerica-east1`).
3. Ative **Authentication** → método **E-mail/senha**.
4. Em **Configurações do projeto → Geral → Seus apps**, registre um app
   Web e copie o objeto `firebaseConfig`.
5. Abra `index.html`, ache o bloco `const firebaseConfig = { ... }`
   (dentro do último `<script type="module">`) e substitua os campos
   `"COLE_AQUI"` pelos valores copiados.
6. Em **Firestore Database → Regras**, cole o conteúdo do arquivo
   `firestore.rules` e publique.

### 2. Criar sua própria conta de admin (passo único, manual)

Como o portal só deixa o *admin* cadastrar novas contas, a primeira conta
(a sua) precisa ser criada direto no console do Firebase:

1. **Authentication → Users → Add user** — coloque seu e-mail e uma
   senha. Depois de criado, copie o **User UID** que aparece na lista.
2. **Firestore Database → Start collection** → ID da coleção: `medicos`.
   Crie um documento com **ID do documento = o UID que você copiou** e
   estes campos:
   - `nome` (string) — seu nome
   - `isAdmin` (boolean) — `true`
   - `ativo` (boolean) — `true`
   - `email` (string) — o mesmo e-mail que você cadastrou

Pronto — a partir daqui, tudo o resto (cadastrar médico, ativar/desativar,
publicar material) é feito direto pela tela do portal, sem precisar
voltar ao console.

### 3. Usar o portal

- Acesse `index.html` (publicado no mesmo lugar que os outros arquivos do
  site) e entre com o e-mail/senha que você criou.
- Aba **Médicos**: cadastre nome, CRM, especialidade, e-mail e uma senha
  temporária — o portal cria a conta de login sozinho. Depois de salvar,
  copie o e-mail/senha mostrados na tela e envie ao médico por um canal
  seguro (WhatsApp, e-mail).
- Aba **Materiais**: cadastre texto direto na página, ou um link de PDF
  (Google Drive/Dropbox com acesso "qualquer pessoa com o link") ou um
  link de vídeo do YouTube/Vimeo (é convertido automaticamente para um
  player embutido). Só materiais marcados como **Publicado** aparecem
  para os médicos.
- Um médico pode ser **desativado** a qualquer momento (ele para de ver
  a biblioteca, mesmo sem apagar a conta).

## Por que os PDFs/vídeos são links externos, e não upload direto

Pra não precisar configurar o Firebase Storage (mais uma peça, com custo
por armazenamento), o portal aponta para links já hospedados em algum
lugar (Google Drive, Dropbox, YouTube não-listado etc.). Se no futuro
fizer sentido subir arquivo direto pelo portal, dá pra adicionar Storage
depois sem afetar o que já existe.

## Sobre o e-mail de recuperação de senha

Hoje o fluxo é: você cria a senha e envia ao médico manualmente. Se um
médico esquecer a senha, mais fácil é você cadastrá-lo de novo com uma
senha nova (o cadastro sobrescreve a conta anterior se usar o mesmo
e-mail é rejeitado pelo Firebase — nesse caso, delete o usuário antigo em
Authentication → Users antes de recriar).
