# 001 - Frontend servido pelo Spring

Data: 2026-11-07
Status: aceita

## Contexto
O front (pasta `frontend/`) era separado: abria direto no navegador ou pela Vercel,
e chamava a API num endereço fixo, `http://localhost:8080/api/fios`.
Isso dava dois problemas:
- Página e API em origens diferentes, então precisava de CORS (e o `CorsConfig` liberava qualquer site).
- Na Vercel não funcionava: o `localhost` era a máquina de quem abria o site, não o servidor.

## Opções consideradas
- A) Colocar o front dentro da aplicação (`src/main/resources/static`): o Spring entrega tudo, um link só.
- B) Front num contêiner separado (nginx) e na Vercel online: precisaria resolver o CORS
  e configurar a URL da API pra cada ambiente.

## Decisão
Opção A. O foco do projeto é aprender Docker na prática, então escolhi o caminho mais simples:
um contêiner e um link só.

## Consequências
- Ganha: Docker mais fácil de configurar, tudo centralizado; sem CORS; a URL `/api/fios`
  funciona em qualquer lugar (PC, Docker, online).
- Perde: aprende menos que na B; mudar só o CSS exige recompilar a aplicação;
  front e back sempre sobem juntos.