# Comandos linux
 Tutoriais

## Criando certificados para apache

### Criando um arquivo com nano

nano san.cnf

### Dentro do arquivo escreva 

[req]
                distinguished_name = req_distinguished_name
                req_extensions = v3_req
                prompt = no
                [req_distinguished_name]
                C = BR
                ST = DF
                L = Brasília
                O = <nomedaempresa>
                OU = <nomeamigaveldeOU>
                CN = <fqdncompleto>
                [v3_req]
                keyUsage = keyEncipherment, dataEncipherment
                extendedKeyUsage = serverAuth
                subjectAltName = @alt_names
                [alt_names]
                DNS.1 = <fqdncompleto>
                DNS.2 = <outrosDNS>

### Criar req utilizando o openssl

openssl req -out <nomeamigavel>.csr -new -newkey rsa:2048 -nodes -keyout <nomeamigavel>.key -config san.cnf

#### visualizar csr com cat

cat <nomeamigavel>.csr


#### faça copia do conteudo do csr
##### caso precise de permissão 
chown <usuario>:<usuario>  <pasta>

#### assine o certificado e copie para dentro da mesma pasta e execute 
##### conveter de para crt
openssl x509 -inform PEM -in <nomeamigavel>.cer -out <nomeamigavel>.crt

#### navegue ate sites avaiçable

cd /etc/apache2/sites-available/

#### crie um novo arquivo 

nano <nomedosite>.conf

#### ou faça uma copia do arquivo <site80>.conf <site443>.conf

cp <site80>.conf <site443>.conf

#### escreva 

<VirtualHost <dnsname>:443>
  ServerName <dnsname>
 

  ## Vhost docroot
  DocumentRoot "/var/www/html/<site>"

  ## Directories, there should at least be a declaration for /var/www/html

  <Directory "/var/www/html">
    Options Indexes FollowSymLinks MultiViews
    AllowOverride None
    Require all granted
  </Directory>

 ## SSL directives
  SSLEngine on
  SSLCertificateFile "/home/josue.bruno/certificados/<site>/<site>.crt"
  SSLCertificateKeyFile   "/home/josue.bruno/certificados/<site>/<site>.key"
  ## Logging
  ErrorLog "/var/log/apache2/<site>.ipea.gov.br-443_error.log"
  ServerSignature Off
  CustomLog "/var/log/apache2/<site>.ipea.gov.br-443_access.log" "combined" env=!viaproxy
  CustomLog "/var/log/apache2/<site>.ipea.gov.br-443_access.log" "forwarded" env=viaproxy
  SetEnvIf X-Forwarded-For "^.*\..*\..*\..*" viaproxy
</VirtualHost>





