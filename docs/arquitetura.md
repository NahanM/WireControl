# Arquitetura do WireControl

Sistema de controle de estoque de fios elétricos.
Java 17 + Spring Boot, PostgreSQL, front em HTML/CSS/JS puro, tudo rodando com Docker.

## Peças
- Navegador: mostra a tela e roda o JavaScript do front
- Aplicação (Spring Boot): recebe os pedidos, aplica as regras e entrega os arquivos do front
- Banco (PostgreSQL): guarda os fios

## Quem fala com quem
Navegador ──porta 8080──▶ app ──db:5432 (rede interna do compose)──▶ db

## Onde cada peça roda
- Navegador: no PC ou celular de quem usa
- Aplicação + arquivos do front: serviço `app` do compose (imagem feita pelo `Dockerfile`)
- Banco: serviço `db` do compose, dados no volume `wirecontrol-pgdata`
- Tudo sobe com `docker compose up --build`. O banco só é acessível pela rede interna.

## Mapa do projeto
    WireControl/
    ├── src/main/java/com/nahanm/wirecontrol/
    │   ├── WireControlApplication.java  → ponto de partida: liga a aplicação
    │   ├── controller/FioController.java → recebe os pedidos da URL (/api/fios) e devolve a resposta
    │   ├── service/FioService.java       → aplica as regras (código repetido, quantidade negativa)
    │   ├── repository/FioRepository.java → busca e guarda no banco
    │   └── model/Fio.java                → o que é um fio (código, descrição, bitola, quantidade) e a tabela `fios`
    ├── src/main/resources/
    │   ├── application.properties  → configurações; lê o banco das variáveis DB_* (sem senha no arquivo)
    │   └── static/                 → o front: index.html (tela), style.css (visual), script.js (chama a API)
    ├── pom.xml              → lista as bibliotecas do projeto e como gerar o .jar (Maven)
    ├── mvnw / mvnw.cmd      → rodam o Maven sem precisar instalar (Linux / Windows)
    ├── Dockerfile           → receita da imagem: compila num estágio, roda só o .jar em outro
    ├── .dockerignore        → o que não vai pro build do Docker
    ├── docker-compose.yml   → sobe o banco e a aplicação juntos
    ├── .env                 → a senha do banco (só no PC, fora do Git)
    ├── .env.example         → modelo do .env, sem senha de verdade
    ├── .gitignore           → o que não vai pro Git
    └── docs/                → esta documentação

## Caminho de um pedido (exemplo: cadastrar um fio)
1. Navegador: o `script.js` manda `POST /api/fios` com os dados do fio
2. Controller: recebe e pergunta ao service se o código já existe
3. Service: confere as regras e manda salvar
4. Repository: grava na tabela `fios` do banco
5. A resposta volta pelo mesmo caminho: 201 (criado) ou 409 (código repetido)