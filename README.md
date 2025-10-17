# Comandos e Conceitos Essenciais de Docker

Este README lista comandos e conceitos importantes para trabalhar com Docker.

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

## ⚙️ Configurações no Dockerfile (Usuário)

Estes comandos são tipicamente utilizados dentro do arquivo `Dockerfile` para criar um grupo e um usuário, garantindo que o container não rode como `root`.

| Comando | Descrição |
| :--- | :--- |
| `RUN addgroup dev && adduser -S -G lucas dev` | Cria o grupo `dev` e adiciona o usuário `lucas` a ele. |
| `USER lucas` | Define que as instruções seguintes e o comando principal do container devem ser executados pelo usuário `lucas`. |


*Lembre-se: `{}` indica um parâmetro que você deve substituir pelo valor real (ex: nome do container, nome da imagem, porta).*