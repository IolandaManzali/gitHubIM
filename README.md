## Executando Oracle Database Free com Docker: Uma Solução Rápida e Simples

### Motivação

A ideia por trás deste processo é disponibilizar um banco de dados **Oracle** de forma rápida e sem complicações. Muitas vezes, configurar um banco de dados Oracle envolve o uso de serviços como a **Oracle Cloud** ou lidar com bancos de dados em outros ambientes, o que pode ser complexo e demorado. Usar **Docker** para rodar o Oracle Database Free localmente permite que você tenha uma solução funcional sem depender de nuvem ou bancos de terceiros, tudo com uma instalação mínima e simplificada. Nosso objetivo é permitir que você tenha um banco Oracle rodando em questão de minutos!

### O que é Docker e contêineres?

**Docker** é uma ferramenta que permite criar e rodar **contêineres**, que são ambientes isolados onde os programas podem ser executados sem interferir no sistema principal. Um contêiner inclui tudo o que o programa precisa para rodar: o sistema operacional, as bibliotecas e o próprio programa. Isso elimina a necessidade de instalar cada componente manualmente no seu sistema.

Pense em um contêiner como uma "miniatura" de um computador que roda dentro do seu computador. Ele tem tudo que o programa (neste caso, o Oracle Database) precisa para funcionar.

### Passos para rodar o Oracle Database Free com Docker

#### 1. **Instalar Docker**
   Docker é a ferramenta principal para rodar os contêineres. Aqui está como instalá-lo:

   - **Windows/Mac**: Baixe e instale o **[Docker Desktop](https://www.docker.com/products/docker-desktop)**. Ele inclui tudo o que você precisa.
   - **Linux**: No terminal, execute os seguintes comandos:
     ```bash
     sudo apt update
     sudo apt install docker.io
     sudo systemctl start docker
     sudo systemctl enable docker
     ```

#### 2. **Instalar Docker Compose**
   O **Docker Compose** é uma ferramenta que permite rodar múltiplos contêineres com um único comando. Aqui está como instalá-lo:

   - **Windows/Mac**: Docker Compose já vem incluso no Docker Desktop.
   - **Linux**: Instale Docker Compose com os seguintes comandos:
     ```bash
     sudo curl -L "https://github.com/docker/compose/releases/download/$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
     sudo chmod +x /usr/local/bin/docker-compose
     ```

#### 3. **Criar um arquivo `docker-compose.yml`**
   O arquivo `docker-compose.yml` descreve como rodar o Oracle Database dentro de um contêiner. Aqui está um exemplo:

   1. Crie uma pasta no seu computador.
   2. Dentro dessa pasta, crie um arquivo chamado `docker-compose.yml` com o seguinte conteúdo:
   
   ```yaml
   version: '3.8'

   services:
     oracle-db:
       image: container-registry.oracle.com/database/free:23.5.0.0-amd64
       container_name: oracle-db
       ports:
         - "1521:1521"  # Porta do Oracle Database
         - "5500:5500"  # Porta do Oracle EM Express
       environment:
         - ORACLE_SID=FREE
         - ORACLE_PDB=FREEPDB1
         - ORACLE_PWD=YourSecurePasswordHere
         - ORACLE_MEMORY=2GB
       volumes:
         - oracle-data:/opt/oracle/oradata
       restart: unless-stopped

   volumes:
     oracle-data:
       driver: local
   ```

   - Substitua `YourSecurePasswordHere` por uma senha segura de sua escolha.
   - Esse arquivo configura o banco Oracle para rodar no contêiner, com os volumes para armazenar dados e portas mapeadas para acessar o banco.

#### 4. **Rodar o banco de dados**
   Agora você está pronto para rodar o Oracle Database. No terminal ou prompt de comando, vá até a pasta onde você salvou o arquivo `docker-compose.yml` e execute o seguinte comando:

   ```bash
   docker-compose up
   ```

   Isso irá baixar a imagem do Oracle Database Free e iniciar o banco de dados em um contêiner. Quando o processo terminar, o banco estará disponível para uso.

### Testar o Banco de Dados

Depois que o banco estiver rodando, é uma boa ideia testar se ele está funcionando corretamente.

#### Verificar se o contêiner está rodando
Execute o seguinte comando no terminal para verificar se o contêiner está ativo:

```bash
docker ps
```

Se o contêiner `oracle-db` estiver listado, isso significa que ele está rodando corretamente.

#### Conectar-se ao banco de dados usando SQL*Plus (modo simples)
Você pode usar a ferramenta **SQL*Plus** para testar o banco de dados diretamente no contêiner.

1. Entre no contêiner:
   ```bash
   docker exec -it oracle-db bash
   ```

2. Conecte-se ao banco de dados como administrador (`SYS`):
   ```bash
   sqlplus sys/YourSecurePasswordHere@localhost:1521/FREEPDB1 as sysdba
   ```

3. Teste o banco de dados executando uma consulta:
   ```sql
   SELECT name FROM v$database;
   ```

   Se você ver o nome do banco de dados, tudo está funcionando corretamente.

#### Usar uma ferramenta gráfica (opcional)
Se você preferir uma interface gráfica para gerenciar o banco de dados, pode usar ferramentas como **DBeaver** ou **Oracle SQL Developer**.

1. **Baixe e instale uma dessas ferramentas** (por exemplo, [DBeaver](https://dbeaver.io/) ou [Oracle SQL Developer](https://www.oracle.com/tools/downloads/sqldev-downloads.html)).
   
2. **Crie uma nova conexão** com as seguintes informações:
   - **Host**: `localhost`
   - **Porta**: `1521`
   - **SID**: `FREEPDB1`
   - **Usuário**: `SYS` (ou `SYSTEM` se preferir)
   - **Senha**: A senha que você definiu no `docker-compose.yml`
   - **Role** (Função): `SYSDBA` (apenas se usar o usuário `SYS`)

3. Teste a conexão e você poderá começar a gerenciar o banco de dados graficamente.

### Conclusão

Usar Docker para rodar o **Oracle Database Free** é a maneira mais rápida e simples de disponibilizar um banco de dados Oracle sem complicações com nuvem ou serviços de terceiros. Com apenas alguns passos, você pode ter um banco de dados totalmente funcional em minutos, pronto para uso local ou desenvolvimento.
