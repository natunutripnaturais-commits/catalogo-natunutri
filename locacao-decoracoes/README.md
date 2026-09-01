# Locação de Decorações

Sistema para uma empresa de locação de peças de decoração para festas.

Este projeto é **totalmente independente** do catálogo Natunutri — vive
nesta pasta apenas até você criar o repositório novo no GitHub. Depois é
só copiar o conteúdo desta pasta para a raiz do repositório novo.

## Arquivos

- **`operacional.html`** — sistema interno da equipe (foco atual). Cadastro
  de peças (com formato/cor/tamanho/modelo), cadastro de clientes, reservas
  com checagem de estoque por data e geração de guia/contrato para
  impressão. Uso restrito à equipe, com login.
- `index.html` — catálogo público para o cliente final navegar e reservar
  pelo WhatsApp (**pausado por enquanto**, mantido aqui para quando você
  quiser retomar essa frente).
- `firestore.rules` — regras de segurança do Firestore para as duas
  frentes (peças/clientes/reservas do sistema interno + kits/disponibilidade
  do catálogo público).

## `operacional.html` — o que já vem pronto

- **Peças**: cadastro com nome, formato, cor, tamanho, modelo, código
  interno, foto (URL), quantidade em estoque, valor de locação e
  observações. Filtros por formato/cor/tamanho/modelo e busca por
  nome/código.
- **Clientes**: cadastro com nome, CPF/CNPJ, telefone, e-mail, endereço e
  observações.
- **Reservas**: escolhe o cliente (ou cadastra um novo na hora), define
  data de retirada/devolução e adiciona quantas peças quiser — o sistema
  mostra quantas unidades de cada peça ainda estão livres naquele período
  (considerando outras reservas já feitas) e impede reservar mais do que
  o estoque disponível.
- **Guia/contrato**: ao salvar a reserva, o sistema monta automaticamente
  uma guia numerada com dados da empresa, do cliente, a lista de peças
  (com valores), o texto do termo de responsabilidade (editável) e um
  espaço em branco para assinatura física do cliente e da loja. O botão
  "Imprimir / salvar como PDF" abre a caixa de impressão do navegador —
  em qualquer computador/tablet, escolher "Salvar como PDF" no destino da
  impressão gera o arquivo PDF do contrato, sem precisar de nenhum serviço
  externo.
- **Configurações**: nome da empresa, CNPJ, endereço, telefone e o texto
  do termo de responsabilidade que aparece em toda guia — ajuste com seu
  contador/advogado antes de usar oficialmente.
- Todo o sistema fica atrás de **login** (e-mail/senha) quando a nuvem
  está configurada — é uma ferramenta interna, não uma página pública.
- **Funciona sem nenhuma configuração**: se o Firebase ainda não estiver
  ligado, o sistema entra sozinho em modo demonstração local (com peças e
  clientes de exemplo), sem pedir login, só para você testar o fluxo
  completo.

## Como testar agora, sem configurar nada

Abra `operacional.html` direto no navegador (duplo clique) ou publique a
pasta em qualquer hospedagem estática. Ele entra automaticamente em modo
demonstração (aviso amarelo no topo) com peças e clientes de exemplo — dá
pra cadastrar peça/cliente, montar uma reserva e gerar a guia em PDF sem
depender de nada externo.

## Como ligar a nuvem (Firebase) — login da equipe + dados salvos de verdade

1. Crie um projeto novo em https://console.firebase.google.com (separado
   do projeto do Natunutri).
2. Ative **Firestore Database** (modo produção) e **Authentication** →
   método **E-mail/senha**.
3. Em Authentication → Users, crie um usuário para cada pessoa da equipe
   que vai usar o sistema (e-mail + senha).
4. Em Configurações do projeto → Seus apps → Web, registre um app e copie
   o objeto `firebaseConfig`.
5. Cole esse objeto em `operacional.html` (e também em `index.html`, se for
   usar o catálogo público), dentro do último `<script type="module">`,
   substituindo os campos `"COLE_AQUI"`.
6. Configure as regras do Firestore (arquivo `firestore.rules` nesta
   pasta) em Firestore Database → Regras.
7. Pronto: ao acessar `operacional.html`, a equipe faz login com o usuário
   criado e os dados passam a ficar salvos na nuvem, sincronizados entre
   qualquer computador/tablet.

### Regras de segurança (`firestore.rules`)

O sistema interno usa quatro coleções, **todas restritas à equipe
autenticada** (leitura e escrita exigem login — é uma ferramenta interna,
sem acesso público):

- `pecas` — catálogo interno de peças.
- `clientes` — cadastro de clientes.
- `reservas` — reservas feitas, com os itens e valores.
- `config` — dados da empresa e texto do termo de responsabilidade.

Se você também usar o catálogo público (`index.html`), ele usa outras
coleções (`kits`, `disponibilidade`, e um `config` separado dele) com
regras mais abertas para leitura — veja os comentários dentro do próprio
`firestore.rules`.

Copie o conteúdo de `firestore.rules` para a aba "Regras" no console do
Firestore.

## Como migrar para o repositório novo do GitHub

1. Crie o repositório na sua conta nova.
2. Copie todos os arquivos desta pasta para a raiz do repositório novo.
3. Faça o primeiro commit e push.
4. (Opcional) Ative o GitHub Pages nas configurações do repositório para
   publicar o sistema direto do GitHub — mas como ele guarda dados de
   clientes, prefira deixar o acesso restrito (link não divulgado, ou uma
   hospedagem com senha) em vez de deixar totalmente público.

## Personalização rápida

- **Dados da empresa e termo de responsabilidade**: pelo próprio sistema,
  aba "Contrato / Config".
- **Peças e clientes de exemplo**: editando `pecasDemo` e `clientesDemo`
  no início do `<script>` de `operacional.html` (só valem enquanto a
  nuvem não está ligada).
- **Cores**: variáveis CSS no topo de cada arquivo (`:root { --roxo: ... }`).

## Limitações conhecidas / próximos passos sugeridos

- O número da guia é sequencial simples (baseado na quantidade de
  reservas já criadas). Para uso com várias pessoas cadastrando reservas
  ao mesmo tempo, o ideal futuro é um contador atômico no Firestore para
  evitar dois contratos com o mesmo número.
- A foto da peça é só uma URL (sem upload de arquivo) — para tirar foto
  direto do celular e já anexar, o próximo passo seria integrar o Firebase
  Storage.
- Não há controle de permissões por usuário (todo login autenticado tem
  acesso total ao sistema) — se no futuro for preciso limitar o que cada
  funcionário pode fazer, isso pede uma camada extra de permissões.
