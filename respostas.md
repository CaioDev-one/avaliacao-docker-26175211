# Respostas · Avaliação Prática de Docker · ViaSerra Transportes (Turma C)

Nome: Caio Cabral Pinto
Matrícula: 26175211
Usuário do GitHub: CaioDev-one
Usuário do Docker Hub: caiodevone

## Parte 1 · Dockerfile do portal

1. Usei a imagem base `nginx:1.27-alpine`. A imagem final do portal apresentou 75,9 MB de uso em disco e 21,8 MB de conteúdo na saída de `docker images`.

2. O Nginx procura os arquivos em `/usr/share/nginx/html/`. Para conferir a presença do `index.html` dentro do container:

   ```bash
   docker compose exec portal ls -l /usr/share/nginx/html/index.html
   ```

## Parte 2 · Docker Hub

3. Nome completo da imagem: `caiodevone/viaserra-portal:1.0-26175211`.

   Link público: https://hub.docker.com/r/caiodevone/viaserra-portal

4. Depois de salvar a alteração no HTML, preciso reconstruir a imagem e enviá-la novamente ao Docker Hub:

   ```bash
   docker build -t caiodevone/viaserra-portal:1.0-26175211 ./portal
   docker push caiodevone/viaserra-portal:1.0-26175211
   ```

## Parte 3 · Página de manutenção

5.

| # | Instrução | O que estava errado | O que você viu acontecer | Como corrigiu |
|---|---|---|---|---|
| 1 | `COPY pagina/ .` | A pasta de origem `pagina/` não existia; a correta era `site/`. | O build falhou com a mensagem `"/pagina": not found`. | Troquei a origem para `site/`. |
| 2 | `CMD ["nginx"]` | O Nginx iniciava em segundo plano, encerrando o processo principal do container. | O container apareceu como `Exited (0)` no `docker ps -a`. | Usei `CMD ["nginx", "-g", "daemon off;"]` para manter o Nginx em primeiro plano. |
| 3 | `COPY site/ .` com `WORKDIR /usr/share/nginx` | O destino era `/usr/share/nginx`, fora da pasta usada pelo Nginx para servir o site. | O navegador mostrou “Welcome to nginx!” em vez da página de manutenção. | Alterei para `COPY site/ /usr/share/nginx/html/`. |

6. O formato é `-p porta_do_host:porta_do_container`. Em `-p 7042:80`, a porta 7042 do computador encaminha para a porta 80 do container. Em `-p 80:7042`, a porta 80 do computador encaminha para a porta 7042 do container. O segundo número é sempre a porta do container. Como nosso Nginx escuta na porta 80, a primeira opção corresponde a essa configuração.

## Parte 4 · Primeiro docker-compose

7. Com as imagens já disponíveis, estes comandos reproduzem a execução dos dois sites, incluindo as portas e a política de reinício:

   ```bash
   docker run -d --name portal --restart unless-stopped -p 8011:80 caiodevone/viaserra-portal:1.0-26175211

   docker run -d --name manutencao --restart unless-stopped -p 7011:80 avaliacao-docker-viaserra-manutencao:latest
   ```

   A imagem da manutenção acima foi construída pelo Compose. O `docker run` não faz o build automaticamente nem cria a rede compartilhada que o Compose cria.

8. O comando que para e remove os dois containers e a rede criada pelo Compose é:

   ```bash
   docker compose down
   ```

## Verificador

9. Código de conclusão impresso pelo verificador:

   ```
   VIASERRA-26175211-05745D86
   ```
