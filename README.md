# Comandos e Conceitos Essenciais de Docker

Este README lista comandos e conceitos importantes para trabalhar com Docker.


## ⚙️ Passo a Passo do Dockerfile, **Exemplo com node e OS Alpine**

Este guia detalha o processo de construção de uma imagem Docker, otimizada para aplicações **Node.js** usando a distribuição **Alpine**, focando em eficiência e tamanho reduzido.

---

### 🏗️ Fases de Construção da Imagem

| Comando | Descrição | Objetivo |
| :--- | :--- | :--- |
| `FROM node:current-alpine3.22` | Define a imagem base a ser utilizada. Utilizamos a versão **Alpine** do Node.js, conhecida por ser **extremamente leve** e segura, reduzindo o tamanho final do container. | **Inicialização da Imagem Base** |
| `WORKDIR /app` | Define o diretório de trabalho (`/app`) dentro do container. Todos os comandos subsequentes (como `COPY` e `RUN`) serão executados a partir deste caminho. | **Configuração do Diretório** |
| `COPY package.json .` | Copia apenas o arquivo `package.json` (e o `yarn.lock` ou `package-lock.json`, se houver) para o diretório `/app` do container. | **Cópia de Dependências** |
| `RUN apk add --no-cache python3 g++ make python3-dev` | **Instala as Ferramentas de Compilação:** O Alpine usa o gerenciador de pacotes `apk`. Este comando instala ferramentas essenciais (`python3`, `g++`, `make`) que são frequentemente necessárias para compilar dependências nativas (como módulos C++) durante o `yarn install`. O `--no-cache` garante que os arquivos de cache de instalação sejam removidos imediatamente. | **Instalação de Binários de Build** |
| `RUN yarn install --production` | Executa a instalação das dependências listadas no `package.json` usando o Yarn. A flag `--production` garante que apenas as dependências de produção sejam instaladas, **excluindo as de desenvolvimento** para manter a imagem enxuta. | **Instalação de Dependências Node** |
| `COPY . .` | Copia o restante dos arquivos da aplicação (código-fonte, assets, etc.) do diretório local (máquina host) para o diretório de trabalho (`/app`) no container. | **Cópia do Código-Fonte** |
| `CMD ["node", "src/index.js"]` | Define o comando padrão que será executado quando o container for iniciado (ou seja, quando `docker run` for chamado). Ele inicia a aplicação Node.js. | **Ponto de Entrada da Aplicação** |
| `EXPOSE 3000` | Documenta que a aplicação dentro do container está escutando na porta **3000**. Isso é apenas informativo; para acessar a aplicação externamente, a porta deve ser mapeada com a flag `-p` no comando `docker run`. | **Declaração de Porta** |

---

## 💡 Por que a Ordem dos Comandos é Importante?

A ordem em que os comandos `COPY` e `RUN` são executados é crucial para o desempenho e otimização do Docker, pois ela afeta o **Cache de Camadas** (Layers Cache):

1.  **Cópia Isolada:** Copiar `package.json` e instalar dependências (`yarn install`) separadamente garante que esta camada (que geralmente é grande) **só será reconstruída** se o arquivo `package.json` mudar.
2.  **Eficiência:** Se apenas o código-fonte (que é copiado na camada `COPY . .` final) for alterado, o Docker reutiliza o cache de todas as camadas anteriores (incluindo o demorado `yarn install`), resultando em **builds muito mais rápidos**.


## 🐳 Comandos Docker

### Construção de Imagens

| Comando | Descrição | Exemplo |
| :--- | :--- | :--- |
| `docker build` | Constrói a imagem da aplicação a partir de um `Dockerfile`. | |
| `-t` | Flag para criar uma **tag** (rótulo) para a imagem (pode ser qualquer nome). | |
| `.` | Indica que a construção deve ocorrer a partir do diretório local. | |
| **Completo** | Constrói a imagem e atribui a tag `hi-docker`. | `docker build -t hi-docker .` |

### Gerenciamento de Imagens

| Comando | Descrição |
| :--- | :--- |
| `docker images` | Lista todas as imagens baixadas ou construídas localmente. |
| `docker pull {nome-do-os}` | Baixa a imagem do Sistema Operacional (OS) desejado. |
| `docker image save -o {nome-do-arquivo}.tar {nome-da-imagem:tag}` | Salva a imagem em um arquivo `.tar` para backup ou transferência. |
| **Exemplo Salvar** | Salva a imagem `app:v1.0` no arquivo `appv1.tar`. | `docker image save -o appv1.tar app:v1.0` |

### Execução e Gerenciamento de Containers

| Comando | Descrição | Exemplo |
| :--- | :--- | :--- |
| `docker run {nome-da-imagem}` | Roda a imagem em um novo container. O nome precisa ser o dado à imagem. | `docker run hi-docker` |
| `docker run -it {nome-do-os}` | Roda o OS desejado em modo interativo (`-it`). Ele é baixado temporariamente e o container, junto com o OS, é apagado ao parar. | |
| `docker run -it {nome do container} sh` | Roda o container e abre o terminal shell (`sh`) dentro dele. | |
| `docker run -dp {porta-máquina}:{porta-container} {nome-da-imagem}` | Roda a aplicação em *daemon* (background, `-d`) e mapeia as portas (`-p`). | `docker run -dp 3000:3000 app` |
| `docker run -d -p 80:3000 --name {nome-container} {nome-repo:tag}` | Redireciona a porta 80 da máquina para a porta 3000 do container e atribui um nome. | `docker run -d -p 80:3000 --name meu-app app:v1` |
| `docker ps` | Verifica os containers que estão em execução. | |
| `docker exec {nome-do-container} {comando}` | Executa um comando Linux dentro de um container já em execução. | `docker exec meu-app ls` |

### Controle de Containers

| Comando | Descrição |
| :--- | :--- |
| `docker start {nome-do-container}` | Inicia um container que já foi criado e está parado. |
| `docker stop {nome-do-container}` | Para um container que está em execução. |
| `docker rm {nome-do-container}` | Remove um container parado. |
| `docker rm -f {nome-do-container}` | Remove um container de forma forçada (útil se estiver em execução e não puder ser parado normalmente). |

### Criando e manipulando Volumes

| Comando | Descrição |
| :--- | :--- |
| `docker volume create {nome que voce vai dar ao volume}` | Cria um volume com o nome desejado. |
| `docker volume inspect {nome que voce vai dar ao volume}` | Vizualiza as informações do volume,onde foi criado,data de criação e etc. |
| `docker run -d -p {associação de porta} --name {nome do container} -v {nome do volume criado}:{diretorio onde ele vai ser adicionado "exemplo: /app/dados"} {nome-repo:tag}` | cria um novo container associando o volume e direcionando o diretorio onde ele vai ficar. |

### Copiando arquivos do container para a maquina Local

| Comando | Descrição |
| :--- | :--- |
| `docker cp {nome do container}:{origem do dado "exemplo: /app/pasta/teste.txt} {destino, se for na mesma pasta que o cmd estiver aberto, pode colocar comente um ponto}` | Copia o arquivo do container para a maquina local. |
| `docker cp {nome do arquivo} {nome do container}:{destino onde vai ser copiado "exemplo: /app/pasta/} ` |Copia os Arquivos locais para o container. |


## ⚙️ Configurações no Dockerfile (Usuário)

Estes comandos são tipicamente utilizados dentro do arquivo `Dockerfile` para criar um grupo e um usuário, garantindo que o container não rode como `root`.

| Comando | Descrição |
| :--- | :--- |
| `RUN addgroup dev && adduser -S -G lucas dev` | Cria o grupo `dev` e adiciona o usuário `lucas` a ele. |
| `USER lucas` | Define que as instruções seguintes e o comando principal do container devem ser executados pelo usuário `lucas`. |


*Lembre-se: `{}` indica um parâmetro que você deve substituir pelo valor real (ex: nome do container, nome da imagem, porta).*