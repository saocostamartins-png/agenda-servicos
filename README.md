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
  nome, tipo, whatsapp
  servicos/{id}    { nome, preco }
  clientes/{id}    { nome, telefone, notas }
  marcacoes/{id}   { data, hora, clienteId, servicoId, notas, status }
  vendas/{id}      { data, clienteId, servicoId, valor, pagamento, marcacaoId }
```

## Firebase Storage — regras de segurança (logótipos)
As regras estão em `storage.rules`. Cola o conteúdo em
Firebase Console → Storage → Regras → Publicar.
Cada negócio só pode escrever na sua própria pasta `logos/{uid}/`; a leitura é pública (para o logótipo aparecer no cabeçalho).
