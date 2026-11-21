# Agenda Serviços

App web multi-cliente de marcações, clientes, serviços e vendas — para barbearias, cabeleireiros, esteticistas e outras atividades de serviços.

## Stack
- Frontend: HTML/CSS/JS puro (sem build step), Firebase SDK via CDN
- Backend: Firebase (Authentication + Firestore)
- Hosting: Vercel (ou qualquer hosting de ficheiros estáticos)

## Deploy
1. Importa este repositório no [vercel.com/new](https://vercel.com/new)
2. Não precisa de configuração de build (é um site estático) — "Deploy" diretamente
3. Cada negócio cria a sua própria conta (login/senha) e só vê os seus próprios dados

## Firestore — regras de segurança
As regras estão em `firestore.rules`. Cola o conteúdo em
Firebase Console → Firestore Database → Regras → Publicar.

## Estrutura de dados
```
negocios/{uid}
  nome, tipo, whatsapp, logoUrl
  horario          { duracaoPadrao, dias: { seg: {aberto, inicio, fim}, ter: {...}, ... } }
  servicos/{id}    { nome, preco, duracaoMin }
  clientes/{id}    { nome, telefone, notas }
  marcacoes/{id}   { data, hora, servicoId, notas, status,
                     clienteId   (marcação interna, feita pelo negócio) |
                     clienteNome, clienteTelefone, origem:"publico"  (marcação feita pelo cliente na página pública) }
  vendas/{id}      { data, servicoId, valor, pagamento, marcacaoId,
                     clienteId | clienteNome, clienteTelefone }
```
O logótipo de cada negócio é guardado como imagem redimensionada (base64) dentro do próprio documento `negocios/{uid}` (campo `logoUrl`) — não usa Firebase Storage, porque o Storage passou a exigir plano de faturação (Blaze) mesmo para uso gratuito.

## Página pública de marcação (`marcar.html`)
Cada negócio tem um link próprio para partilhar com os clientes:
```
https://<o-teu-site>.vercel.app/marcar.html?negocio=<uid-do-negocio>
```
Este link está disponível e pronto a copiar dentro da app, no separador **Marcações**. O cliente, sem precisar de conta:
1. Escolhe o serviço (vê duração e preço).
2. Escolhe o dia — só aparecem horários dentro do horário de funcionamento definido em **Definições** para esse dia da semana.
3. Vê só os horários ainda livres (os já ocupados por outras marcações, tendo em conta a duração do serviço, são automaticamente escondidos — não há sobreposições).
4. Indica nome e telefone e confirma — a marcação aparece de imediato na app do negócio, assinalada como "online".

As regras do Firestore (`firestore.rules`) permitem leitura pública dos dados do negócio, serviços e marcações (para calcular disponibilidade), e permitem que qualquer pessoa **crie** uma marcação (com campos limitados), mas só o dono do negócio pode editar, cancelar ou concluir marcações, e os dados de clientes e vendas continuam sempre privados.

⚠️ Depois de atualizar este ficheiro, é preciso colar o novo conteúdo de `firestore.rules` na Firebase Console → Firestore Database → Regras → Publicar (substitui as regras antigas, mais restritivas).
