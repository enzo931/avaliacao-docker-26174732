# Respostas · Avaliação Prática de Docker · ViaSerra Transportes (Turma C)

Nome: Enzo Manzoni Araújo 
Matrícula: 26174732
Usuário do GitHub: enzo931
Usuário do Docker Hub: enzomanzoni001

Responda com as suas palavras e com o que aconteceu na SUA máquina. Resposta curta e certa vale mais
do que texto longo copiado. Resposta que contradiz o seu próprio Dockerfile vale zero.

## Parte 1 · Dockerfile do portal

1. Qual imagem base você usou e qual o tamanho final da imagem do portal (saída de `docker images`)?

Usei a imagem base oficial nginx:1.27-alpine.
O tamanho final da imagem é de aproximadamente 21 MB

2. Em qual pasta do container o Nginx procura os arquivos do site? Mostre o comando que você usou para
   conferir que o `index.html` está lá dentro.

   O Nginx procura os arquivos estáticos do site no diretório /usr/share/nginx/html.  

   O comando utilizado para verificar a presença do arquivo index.html dentro do container de teste foi:

   docker exec teste-portal ls -l /usr/share/nginx/html

   (Ou alternativamente abrindo um terminal interativo dentro do container: docker exec -it teste-portal sh seguido de ls /usr/share/nginx/html).

## Parte 2 · Docker Hub

3. Nome completo da imagem publicada e link público do repositório no Docker Hub.

enzomanzoni001/viaserra-portal:1.0-26174732
https://hub.docker.com/r/enzomanzoni001/viaserra-portal

4. Se você mudar o HTML, quais comandos precisa rodar para que a versão nova chegue ao Docker Hub?

Após alterar o código HTML, é necessário reconstruir a imagem local e em seguida realizar o push para o Docker Hub executando os seguintes comandos:   

Bashdocker build -t enzomanzoni001/viaserra-portal:1.0-26174732 ./portal
docker push enzomanzoni001/viaserra-portal:1.0-26174732

## Parte 3 · Página de manutenção

5. Preencha uma linha por defeito encontrado. Defeito inexistente listado aqui desconta pontos.

O Dockerfile original deixado pelo fornecedor era:

FROM nginx:1.27-alpine
WORKDIR /usr/share/nginx
   [cite: 4]

Tabela de defeitos corrigidos:

| # | Instrução | O que estava errado | O que você viu acontecer | Como corrigiu |
|---|---|---|---|---|
| 1 | Sem instrução COPY | O Dockerfile não copiava os arquivos do site de manutenção para dentro da imagem. | O container subia, mas ao acessar o navegador aparecia a página padrão "Welcome to nginx!" em vez da página de manutenção. | Adicionei a instrução COPY site/ /usr/share/nginx/html/ para copiar os arquivos do site. |
| 2 | WORKDIR /usr/share/nginx | Definia o diretório de trabalho para /usr/share/nginx, alterando a estrutura padrão de execução do Nginx. | O container fechava imediatamente após subir ou apresentava erro ao carregar as configurações do Nginx. | Removi a linha WORKDIR /usr/share/nginx do Dockerfile. |
| 3 | Sem instrução EXPOSE | A porta 80 do container não estava explicitamente declarada. | Dificuldade na documentação interna da imagem sobre qual porta o serviço escuta por padrão. | Adicionei a instrução EXPOSE 80 no Dockerfile. 

6. Qual a diferença entre `-p 7042:80` e `-p 80:7042` no `docker run`? Qual dos dois números é a porta do container?

A sintaxe do parâmetro -p no Docker é sempre -p PORTA_DO_HOST:PORTA_DO_CONTAINER.  

-p 7042:80: Mapeia a porta 7042 da sua máquina física (host) para a porta 80 do container. Ou seja, você acessa a aplicação digitando http://localhost:7042 no navegador.  

-p 80:7042: Tenta mapear a porta 80 da sua máquina física (host) para a porta 7042 do container. Como o Nginx por padrão escuta na porta 80 internamente, essa configuração fará com que o tráfego chegue a uma porta do container onde não há nenhum serviço escutando.

Porta do container: Em ambos os casos, o segundo número (após os dois pontos :) representa a porta interna do container. No mapeamento correto (-p 7042:80), a porta do container é a 80.

## Parte 4 · Primeiro docker-compose

7. Escreva os dois comandos `docker run` que fariam o mesmo que o seu `docker-compose.yml`.

Os dois comandos equivalentes, com política de reinicialização (`--restart unless-stopped`), mapeamento de portas da sua matrícula e nomes de contêineres, são:

# Comando para subir o portal:
docker run -d --name portal --restart unless-stopped -p 8032:80 enzomanzoni001/viaserra-portal:1.0-26174732

# Comando para subir a manutenção:
docker run -d --name manutencao --restart unless-stopped -p 7032:80 manutencao:26174732

8. Qual comando derruba os dois containers de uma vez?

O comando é:
docker compose down

## Verificador

9. Código de conclusão impresso pelo verificador:

```
(cole aqui)
```
