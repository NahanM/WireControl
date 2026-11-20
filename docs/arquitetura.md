# Arquitetura do WireControl

## Peças
- Navegador: mostra a tela e roda o JavaScript do front
- Aplicação (Spring Boot): recebe os pedidos, aplica as regras e entrega os arquivos do front
- Banco (PostgreSQL): guarda os fios

## Quem fala com quem
Navegador ──porta 8080──▶ Aplicação ──porta 5432──▶ Banco

## Onde cada peça roda (Fase 3)
- Navegador: no PC de quem usa (Chrome)
- Aplicação + arquivos do front: contêiner `wirecontrol-app` (imagem `wirecontrol`)
- Banco: contêiner `wirecontrol-db`, com os dados no volume `wirecontrol-pgdata`
- Hoje sobem com dois `docker run` separados. Na Fase 4, um `docker compose up` sobe os dois.