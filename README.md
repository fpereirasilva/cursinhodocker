# 🐋 Guia Completo: Hospedagem Web com Docker, Nginx Proxy Manager e WordPress

Este guia abrangente ensina como configurar múltiplos sites WordPress em um único servidor utilizando Docker, com suporte a HTTPS via Nginx Proxy Manager e Let's Encrypt. Ideal para iniciantes e profissionais que desejam hospedar seus sites de forma eficiente e segura.

## 📑 Sumário
- [Introdução](#introdução)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Pré-requisitos](#pré-requisitos)
- [Instalação nos Sistemas Operacionais](#instalação-nos-sistemas-operacionais)
  - [Ubuntu/Debian](#ubuntudebian)
  - [Rocky Linux](#rocky-linux)
  - [OpenSUSE Leap](#opensuse-leap)
- [Configuração do Firewall](#configuração-do-firewall)
- [Configuração do Proxy Reverso](#configuração-do-proxy-reverso)
- [Criação de Certificados com Let's Encrypt (HTTPS)](#criação-de-certificados-com-lets-encrypt-https)
- [Configuração do WordPress (Múltiplos Sites)](#configuração-do-wordpress-múltiplos-sites)
- [Configuração do Portainer](#configuração-do-portainer)
- [Configuração de Backups](#configuração-de-backups)
- [Administração no Dia a Dia](#administração-no-dia-a-dia)

## 📖 Introdução
Este guia foi desenvolvido para auxiliar na configuração de um ambiente completo de hospedagem de sites WordPress com HTTPS. As instruções são detalhadas para que qualquer pessoa, mesmo com conhecimentos básicos de Linux, consiga seguir o passo a passo.

## 🔧 Tecnologias Utilizadas

| Tecnologia            | O que é                        | Para que serve                   |
|-----------------------|---------------------------------|-----------------------------------|
| Docker                | Plataforma de containerização  | Criar e gerenciar ambientes portáteis |
| Nginx Proxy Manager   | Servidor proxy reverso         | Gerenciar múltiplos sites e SSL  |
| Let's Encrypt         | Certificados SSL gratuitos     | Proteger sites com HTTPS         |
| WordPress             | CMS para criação de sites      | Gerenciar conteúdos online       |
| Portainer             | Interface web para Docker      | Simplificar a administração de containers |

## ✅ Pré-requisitos
Antes de começar, certifique-se de:
- Ter um domínio apontando para o IP do servidor.
- Ter acesso root ou sudo ao servidor.
- Possuir conhecimento básico de Linux e terminal.
- Ter um servidor com pelo menos **1GB de RAM** e **20GB de espaço em disco**.
- Ter uma conexão estável com a internet no servidor.

## 🚀 Instalação nos Sistemas Operacionais
### Ubuntu/Debian
```bash
# Atualizar o sistema
sudo apt update && sudo apt upgrade -y

# Instalar pacotes necessários
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common

# Adicionar a chave GPG do Docker
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Adicionar o repositório Docker
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list

# Atualizar repositórios e instalar o Docker
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Iniciar o Docker
sudo systemctl enable --now docker

# Verificar instalação
docker --version
docker compose version
```

### Rocky Linux
```bash
# Atualizar o sistema
sudo dnf update -y

# Adicionar o repositório Docker
sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo

# Instalar o Docker
sudo dnf install -y docker-ce docker-ce-cli containerd.io

# Iniciar o Docker
sudo systemctl enable --now docker

# Verificar instalação
docker --version
```

### OpenSUSE Leap
```bash
# Atualizar o sistema
sudo zypper refresh

# Instalar o Docker
sudo zypper install -y docker docker-compose

# Iniciar o Docker
sudo systemctl enable --now docker

# Verificar instalação
docker --version
docker-compose --version
```

## 🔒 Configuração do Firewall
### Ubuntu/Debian (UFW)
```bash
# Liberar portas HTTP, HTTPS e Portainer
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 9000/tcp

# Liberar acesso SSH
sudo ufw allow ssh

# Ativar o firewall
sudo ufw enable

# Verificar regras do firewall
sudo ufw status
```

### Rocky Linux/OpenSUSE Leap (FirewallD)
```bash
# Liberar portas HTTP, HTTPS e Portainer
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --permanent --add-port=443/tcp
sudo firewall-cmd --permanent --add-port=9000/tcp

# Recarregar as regras do firewall
sudo firewall-cmd --reload

# Verificar regras do firewall
sudo firewall-cmd --list-all
```

## 🔄 Configuração do Proxy Reverso
Crie o diretório para os arquivos do proxy reverso:
```bash
mkdir -p ~/docker/nginx-proxy
cd ~/docker/nginx-proxy
```

Crie o arquivo `docker-compose.yml`:
```yaml
version: '3.8'
services:
  app:
    image: jc21/nginx-proxy-manager:latest
    container_name: nginx-proxy-manager
    restart: always
    ports:
      - "80:80"
      - "443:443"
      - "81:81"
    environment:
      DB_MYSQL_HOST: "db"
      DB_MYSQL_PORT: 3306
      DB_MYSQL_USER: "npm"
      DB_MYSQL_PASSWORD: "npm-password"
      DB_MYSQL_NAME: "npm"
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt

  db:
    image: mariadb:latest
    container_name: nginx-proxy-db
    environment:
      MYSQL_ROOT_PASSWORD: "root-password"
      MYSQL_DATABASE: "npm"
      MYSQL_USER: "npm"
      MYSQL_PASSWORD: "npm-password"
    volumes:
      - ./db:/var/lib/mysql
```

Suba os containers:
```bash
docker-compose up -d
```

## 🌐 Criação de Certificados com Let's Encrypt (HTTPS)
1. Acesse o Nginx Proxy Manager em: `http://SEU_IP:81`.
2. Use as credenciais padrão:
   - Usuário: `admin@example.com`
   - Senha: `changeme`
3. Configure seus domínios e habilite HTTPS seguindo os passos descritos acima.

## 📝 Configuração do WordPress (Múltiplos Sites)
Crie diretórios e configure cada site conforme o exemplo abaixo. Mais detalhes estão no guia completo acima.

## ⚙️ Configuração do Portainer
Configure o Portainer para gerenciar seus containers Docker de forma fácil.

## 🛡️ Configuração de Backups
Automatize backups para garantir segurança e disponibilidade.

## 🛠️ Administração no Dia a Dia
Inclui monitoramento, atualizações e resolução de problemas básicos.

---

Siga este guia regularmente para manter um ambiente seguro e funcional. Para dúvidas, contribuições ou suporte, entre em contato através do repositório!
