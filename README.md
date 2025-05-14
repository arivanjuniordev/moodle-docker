
# Documentação de Instalação do Moodle com Docker

Este guia fornece instruções passo a passo para configurar um ambiente Moodle usando Docker, incluindo os arquivos de configuração necessários e comandos básicos de gerenciamento.

## Pré-requisitos
- Docker e Docker Compose instalados.
- Conhecimento básico de comandos Docker.
- Permissões suficientes para criar diretórios e gerenciar contêineres.

---

## 1. Estrutura de Diretórios
Garanta que você tenha a seguinte estrutura de diretórios para armazenar seus arquivos de configuração e volumes persistentes:

```
./moodle-docker/
├── docker-compose.yml
├── .env
```
---

## 2. Arquivo .env
Crie um arquivo `.env` no diretório raiz `moodle-docker/` com o seguinte conteúdo:

```env
# Configuração do MariaDB
MARIADB_ROOT_PASSWORD=rootpass123
MARIADB_USER=bn_moodle
MARIADB_PASSWORD=moodlepass123
MARIADB_DATABASE=bitnami_moodle

# Configuração do Moodle
MOODLE_USERNAME=admin
MOODLE_PASSWORD=admin123
MOODLE_EMAIL=admin@example.com
MOODLE_DATABASE_HOST=mariadb
MOODLE_DATABASE_PORT_NUMBER=3306
MOODLE_DATABASE_NAME=bitnami_moodle

# Configuração do Apache
APACHE_ENABLE_HTTPS=false
APACHE_SERVER_NAME=localhost
APACHE_SERVER_ADMIN=admin@example.com

# Configuração do Site Moodle
MOODLE_SITE_NAME="Verbo da Vida"
MOODLE_SKIP_BOOTSTRAP=no
```

---

## 3. Arquivo docker-compose.yml
Crie um arquivo `docker-compose.yml` com o seguinte conteúdo:

```yaml
services:
  mariadb:
    image: bitnami/mariadb:latest
    container_name: mariadb
    restart: unless-stopped
    environment:
      - MARIADB_ROOT_PASSWORD=${MARIADB_ROOT_PASSWORD}
      - MARIADB_USER=${MARIADB_USER}
      - MARIADB_PASSWORD=${MARIADB_PASSWORD}
      - MARIADB_DATABASE=${MARIADB_DATABASE}
    volumes:
      - mariadb_data:/bitnami/mariadb

  moodle:
    image: bitnami/moodle:latest
    container_name: moodle
    restart: unless-stopped
    environment:
      - MOODLE_USERNAME=${MOODLE_USERNAME}
      - MOODLE_PASSWORD=${MOODLE_PASSWORD}
      - MOODLE_EMAIL=${MOODLE_EMAIL}
      - MOODLE_DATABASE_HOST=${MOODLE_DATABASE_HOST}
      - MOODLE_DATABASE_PORT_NUMBER=${MOODLE_DATABASE_PORT_NUMBER}
      - MOODLE_DATABASE_USER=${MARIADB_USER}
      - MOODLE_DATABASE_PASSWORD=${MARIADB_PASSWORD}
      - MOODLE_DATABASE_NAME=${MARIADB_DATABASE}
      - APACHE_ENABLE_HTTPS=${APACHE_ENABLE_HTTPS}
      - APACHE_SERVER_NAME=${APACHE_SERVER_NAME}
      - APACHE_SERVER_ADMIN=${APACHE_SERVER_ADMIN}
      - MOODLE_SITE_NAME=${MOODLE_SITE_NAME}
      - MOODLE_SKIP_BOOTSTRAP=${MOODLE_SKIP_BOOTSTRAP}
      - MOODLE_LANGUAGE=pt_br
    ports:
      - "8080:8080"
    depends_on:
      - mariadb
    volumes:
      - moodle_data:/bitnami/moodle
      - moodledata_data:/bitnami/moodledata
      - apache_conf_data:/bitnami/apache/conf

volumes:
  mariadb_data:
  moodle_data:
  moodledata_data:
  apache_conf_data:
```

---

## 4. Construindo e Iniciando os Contêineres
Execute os seguintes comandos para construir e iniciar os contêineres:

```bash
cd moodle-docker
docker-compose down -v
docker-compose up -d
```

Verifique se os contêineres estão em execução:

```bash
docker ps
```

---

## 5. Acessando o Moodle
Acesse `http://localhost:8080` no seu navegador e faça login com as credenciais do administrador:

- **Usuário:** admin
- **Senha:** admin123

---

## 6. Configurações Adicionais
- Altere a senha padrão do administrador para aumentar a segurança.
- Atualize o nome e descrição do site no painel de administração do Moodle.
- Configure o SMTP para notificações por e-mail.

---

## 7. Gerenciamento dos Contêineres
Para parar os contêineres:

```bash
docker-compose down
```

Para iniciá-los novamente:

```bash
docker-compose up -d
```

Para ver os logs:

```bash
docker-compose logs moodle
```

---

## 8. Resolução de Problemas
Se encontrar problemas, verifique:
- Conectividade com o banco de dados.
- Permissões dos diretórios.
- Configurações das variáveis de ambiente.

---

## 9. Conclusão
Seu Moodle agora está configurado e funcionando em um ambiente Docker. Personalize conforme necessário para sua instituição ou organização.
