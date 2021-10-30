## INSTALAÇÃO DE SERVIDOR  WORDPRESS 

Putty e um programa usado para realizar conexoes ssh em servidores. este acesso e feito de maneira remota e sem a necessidade de um usuario estar presencialmente conectado a maquina e sendo remotamente configurado. 
o putty e uma das aplicacoes mais usadas no mundo e entre suas principais caracteristicas que o popularizam estao por exemplo:
a sua seguranca baseada em criptografia que garante seguranca ao usuario e ao servidor evitando acessos indesejamveis.
 Outra caracteristica importante seria  a possibilidade de editar , adicionar remover e configurar qualquer dado sendo possivel transferir dados de quaisquer natureza com uma alta confiabilidade e tudo isso utilizando um software gratuito e com compatibilidade entre windows e linux.

Como configurar o PUTTY  (Passo a passo utilizando Putty SSH como conector seguro de terminal para realizar o deploy de uma aplicacao de servidor gerenciado em repositorio Git )
O programa pode ser baixado atravez do site oficial da aplicacao em putty.org e ele oferece opcoes para windows tanto 32 como 64 bits e para linux.
Sera utilizado tambem para o exemplo a aplicacao docker quer simulara a maquina virtual para o projeto.
Após a instalacao o programa putty abre em modo de configuracao onde podemos colocar os dados da conxao que iremos realizar para acessar o terminal da maquina virtual  como o endereco de ip e porta, assim como usuario e senha atravez putty.
Acessar terminal da maquina virtual privada, que pode ser de diferents hospedagens, tais como google, amazon, etc.
pelo Putty utilizando SSH -> Secure Shell ->  Terminal Seguro
no terminal seguro após a conexao com o servidor já com ubuntu instalado deve se seguir uma sequencia de scripts de shell do linux a fim de instalar aplicacoes diretamente pelo putty seguindo uma sequencia: a atualizacao da distribuicao, em seguida  easyengine , mysql e wordpress.  

## 1

``` sh
sudo -i
cd /
apt-get update && apt-get upgrade && apt-get dist-upgrade
```


## 2
``` sh
- wget -qO ee rt.cx/ee && sudo bash ee
```

## 3
``` sh
- nano /etc/ee/ee.conf


Perguntar nome MySQL db  <br>
db-name = **True**

Perguntar user MySQL db  <br>
db-user =  **True**

Perguntar user name WordPress <br>
user = **useradmin**

Senha WordPress <br>
password = **root**

EMail WordPress <br>
email = **email**

```
## 4
``` sh
- ee site create bruno.com.br --wpfc
```

## 5
``` sh
- nano /var/www/bruno.com.br/wp-config.php
```
  ``` php
  define( 'WP_MEMORY_LIMIT', '128M' );
  define ( 'AUTOMATIC_UPDATER_DISABLED', true );
  ```

## 6  pluggin generico eccomerce
``` sh
- nano /etc/nginx/common/wpfc-woocommerce.conf
```
``` php
# configuracao WPFC-WOOCOMMERCE NGINX 

set $skip_cache 0;


if ($request_method = POST) {
     set $skip_cache 1;
}

if ($query_string != "") {
     set $skip_cache 1;
}


 if ($request_uri ~* "(/carrinho.*|/minha-conta.*|/finalizar-compra.*|/addons.*|/wp-admin/|/xmlrpc.php|wp-.*.php|/feed/|index.php|sitemap(_index)?.xml|[a-z0-9_-]+-sitemap([0-9]+)?.xml)") {
     set $skip_cache 1;
}


if ($http_cookie ~* "comment_author|wordpress_[a-f0-9]+|wp-postpass|wordpress_no_cache|wordpress_logged_in") {
     set $skip_cache 1;
}


location / {
     try_files $uri $uri/ /index.php?$args;
}

location ~ \.php$ {
     set $rt_session "";

     if ($http_cookie ~* "wp_woocommerce_session_[^=]*=([^%]+)%7C") {
     set $rt_session wp_woocommerce_session_$1;
     }

     if ($skip_cache = 0 ) {
          more_clear_headers "Set-Cookie*";
          set $rt_session "";
     }

     fastcgi_cache_key "$scheme$request_method$host$request_uri$rt_session";

     try_files $uri =404;
     include fastcgi_params;

     fastcgi_pass php;

     fastcgi_cache_bypass $skip_cache;
     fastcgi_no_cache $skip_cache;

     fastcgi_cache WORDPRESS;
     fastcgi_cache_valid 60m;
}

location ~ /purge(/.*) {
     fastcgi_cache_purge WORDPRESS "$scheme$request_method$host$1";
}
```



## 7
``` sh
- ee site edit bruno.com.br
  ```
  ``` php
  include common/wpfc-woocommerce.conf;
  ```
  ``` sh
- chown -R www-data:www-data /var/www/bruno.com.br/htdocs
  ```
  
  
  
  
Alem disso o terminal seguro permite a conexao e possibilita a clonagem da URL do repositorio git e para isso e necessario a instalacao previa da ferramenta git na maquina virtual.
O repositorio deve ser clonado utilizando o comando GIT CLONE "URL"

