<h1 align="center">Configurando um Proxy em sua VPS </h1>



## Criando um Proxy HTTP com Autenticação no Ubuntu 20.04

Guia para a criação de um servidor proxy HTTP com autenticação de usuário e senha usando o Squid, uma das soluções mais populares para este propósito.
1. Atualizando o sistema
Primeiro, vamos atualizar o sistema:
```
sudo apt update
```


```
sudo apt upgrade -y
```


2. Instalando o Squid

```
sudo apt install squid apache2-utils -y
```


3. Fazendo backup da configuração original
```
sudo cp /etc/squid/squid.conf /etc/squid/squid.conf.original
```


5. Configurando o Squid com autenticação
```
sudo nano /etc/squid/squid.conf
```


Substitua o conteúdo do arquivo com:

Config1:
* Mais minimalista e direta
* Foca apenas na autenticação e controle de cabeçalhos
```
# Porta para o proxy (pode alterar se desejar)
http_port 3128

# Autenticação básica
auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic realm Proxy
acl authenticated proxy_auth REQUIRED
http_access allow authenticated
http_access deny all

# Configurações de performance
forwarded_for off
via off
request_header_access Allow allow all
request_header_access Authorization allow all
request_header_access WWW-Authenticate allow all
request_header_access Proxy-Authorization allow all
request_header_access Proxy-Authenticate allow all
request_header_access Cache-Control allow all
request_header_access Content-Encoding allow all
request_header_access Content-Length allow all
request_header_access Content-Type allow all
request_header_access Date allow all
request_header_access Expires allow all
request_header_access Host allow all
request_header_access If-Modified-Since allow all
request_header_access Last-Modified allow all
request_header_access Location allow all
request_header_access Pragma allow all
request_header_access Accept allow all
request_header_access Accept-Charset allow all
request_header_access Accept-Encoding allow all
request_header_access Accept-Language allow all
request_header_access Content-Language allow all
request_header_access Mime-Version allow all
request_header_access Retry-After allow all
request_header_access Title allow all
request_header_access Connection allow all
request_header_access Proxy-Connection allow all
request_header_access User-Agent allow all
request_header_access Cookie allow all
request_header_access All deny all
```
ou

Config2:
* Mais completa e estruturada
* Inclui configurações de cache
* Define ACLs de segurança para portas permitidas
* Permite explicitamente o localhost

```
# Porta para aceitar conexões de todas as interfaces
http_port 0.0.0.0:3128

# Configuração de autenticação básica
auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic realm Proxy Autenticado
auth_param basic credentialsttl 2 hours

# Definir ACLs
acl authenticated proxy_auth REQUIRED
acl SSL_ports port 443
acl Safe_ports port 80          # http
acl Safe_ports port 21          # ftp
acl Safe_ports port 443         # https
acl Safe_ports port 1025-65535  # portas não privilegiadas

# Regras de acesso
http_access deny !Safe_ports
http_access deny CONNECT !SSL_ports
http_access allow localhost
http_access allow authenticated
http_access deny all

# Configuração de cache
cache_dir ufs /var/spool/squid 100 16 256
coredump_dir /var/spool/squid

refresh_pattern ^ftp:           1440    20%     10080
refresh_pattern -i (/cgi-bin/|\?) 0     0%      0
refresh_pattern .               0       20%     4320
```
Config Recomendada:
* Você pode combinar os melhores aspectos das duas configurações
* Esta configuração híbrida oferece o melhor dos dois mundos: segurança, autenticação, privacidade e desempenho.
```
# Porta para o proxy
http_port 0.0.0.0:3128

# Configuração de autenticação básica
auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic realm Proxy Autenticado
auth_param basic credentialsttl 2 hours

# Definir ACLs
acl authenticated proxy_auth REQUIRED
acl SSL_ports port 443
acl Safe_ports port 80          # http
acl Safe_ports port 21          # ftp
acl Safe_ports port 443         # https
acl Safe_ports port 1025-65535  # portas não privilegiadas

# Regras de acesso
http_access deny !Safe_ports
http_access deny CONNECT !SSL_ports
http_access allow localhost
http_access allow authenticated
http_access deny all

# Configurações de privacidade
forwarded_for off
via off

# Configuração de cache
cache_dir ufs /var/spool/squid 100 16 256
coredump_dir /var/spool/squid

refresh_pattern ^ftp:           1440    20%     10080
refresh_pattern -i (/cgi-bin/|\?) 0     0%      0
refresh_pattern .               0       20%     4320

# Permissões de cabeçalhos mais importantes
request_header_access Allow allow all
request_header_access Authorization allow all
request_header_access WWW-Authenticate allow all
request_header_access Proxy-Authorization allow all
request_header_access Proxy-Authenticate allow all
request_header_access Host allow all
request_header_access User-Agent allow all

```

5 criação de usuário e senha:

```
sudo touch /etc/squid/passwd
```

5.1 Criando usuário e senha para o proxy

Substitua "seuusuario" pelo nome de usuário desejado:

```
sudo htpasswd -c /etc/squid/passwd seuusuario
```

5.2 Definir permissões corretas

```
sudo chown proxy:proxy /etc/squid/passwd
```

6. Configurando o firewall para permitir acesso à porta do proxy
```
sudo iptables -A INPUT -p tcp --dport 3128 -j ACCEPT
```
```
sudo netfilter-persistent save
```
ou

```
sudo ufw allow 3128/tcp
```


```
sudo ufw enable
```


7. Reiniciando o Squid
```
sudo systemctl restart squid
```


9. Verificando status do serviço
```
sudo systemctl status squid
```


# Como usar o proxy

Agora você pode acessar seu proxy usando:
Endereço: http://seu_ip_do_servidor:3128
Autenticação: seu_usuario / sua_senha
Testando o proxy
Para testar, pode usar o comando curl:
curl -x http://seuusuario:suasenha@seu_ip_do_servidor:3128 https://ipinfo.io/json


Se preferir uma porta diferente de 3128, basta modificar o valor de http_port no arquivo de configuração do Squid.


## Consultoria e contato:

    Rafael Oliveira - GESTÃO MASTER

    Fone: 12 99642-8123(WhatsApp)

    Contribua com um café: ChavePix Celular: 12996428123


Todos os direitos reservados a https://gestaomaster.pro
