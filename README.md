# Instalação do Magento 2.3.3

Olá! Siga o passo à passo para rodar o projeto em seu ambiente. Estou disponibilizando o dump do banco para demonstração do conteúdo cadastrado.

[Clique aqui para baixar o Banco](https://drive.google.com/file/d/1_2s3R-rXA6_ysxkpXwY6UycYx3xg-X-5/view?usp=sharing%29)

Para testes em localhost recomendo configurar o seu webserver com um virtual host.
Utilizando o Apache adicionei a seguinte configuração:

/etc/apache2/sites-available/000-default.conf:

        <VirtualHost *:80>
	       ServerName dev.magento2.com.br
	       ServerAlias dev.magento2.com.br
	       ServerAdmin webmaster@localhost
	       DocumentRoot /var/www/html/magento2/pub
               ErrorLog ${APACHE_LOG_DIR}/error.log
               CustomLog ${APACHE_LOG_DIR}/access.log combined
              <Directory "/var/www/html">
                 AllowOverride all
               </Directory>
        </VirtualHost>

Caso o endereço que você configurar já exista na web, adicione-o ao arquivo de hosts de seu ambiente:

    127.0.0.1   dev.magento2.com.br

## Instalação
### 1- Clone o repositório

    git clone https://github.com/olucasu/magento2.git

### 2 - Entre na pasta criada

    cd magento2
    
### 3 - Instale as dependências via COMPOSER

    composer install
### 4 - Configure as permissões de pasta
Dentro do diretório do projeto rode:

    find var generated vendor pub/static pub/media app/etc -type f -exec chmod g+w {} +    
    find var generated vendor pub/static pub/media app/etc -type d -exec chmod g+ws {} +
    chown -R :www-data . 
    chmod u+x bin/magento

 Pronto você já pode instalar o magento2!
 Segundo a documentação do projeto, o magento possui duas formas de instalação.
 
-   Linha de comando
-   Web Setup Wizard

Contudo, afim de que você utilize o [banco](https://drive.google.com/file/d/1_2s3R-rXA6_ysxkpXwY6UycYx3xg-X-5/view?usp=sharing%29) que disponibilizei, vamos precisar realizar mais alguns passos: 

### 5 - Configure o arquivo env.php

O arquivo env não é versionado, logo você vai precisar criá-lo em 

    [diretorio-do-seu-projeto]/app/etc/

Caso necessário crie e edite o arquivo com as permissões de root:
Entre na pasta `app/etc`

    sudo touch env.php


Cole o conteúdo abaixo, e configure o acesso ao seu banco no índice do array "db";



```php
<?php
return [
    'backend' => [
        'frontName' => 'admin_8xq1vd'
    ],
    'crypt' => [
        'key' => '78739dc45a24d9f30cf9811c200c1b08'
    ],
    'db' => [
        'table_prefix' => '',
        'connection' => [
            'default' => [
                'host' => 'localhost',
                'dbname' => 'magento2',
                'username' => 'magento',
                'password' => 'password',
                'model' => 'mysql4',
                'engine' => 'innodb',
                'initStatements' => 'SET NAMES utf8;',
                'active' => '1'
            ]
        ]
    ],



    'resource' => [
        'default_setup' => [
            'connection' => 'default'
        ]
    ],

    'x-frame-options' => 'SAMEORIGIN',
    'MAGE_MODE' => 'production',
    'session' => [
        'save' => 'files'
    ],
    'cache' => [
        'frontend' => [
            'default' => [
                'id_prefix' => '4cf_'
            ],
    '       page_cache' => [
                'id_prefix' => '4cf_'
            ]
        ]
    ],
    'lock' => [
        'provider' => 'db',
        'config' => [
            'prefix' => null
        ]
    ],
    'cache_types' => [
        'config' => 1,
        'layout' => 1,
        'block_html' => 1,
        'collections' => 1,
        'reflection' => 1,
        'db_ddl' => 1,
        'compiled_config' => 1,
        'eav' => 1,
        'customer_notification' => 1,
        'config_integration' => 1,
        'config_integration_api' => 1,
        'google_product' => 1,
        'full_page' => 1,
        'config_webservice' => 1,
        'translate' => 1,
        'vertex' => 1
    ],
    'install' => [
        'date' => 'Thu, 31 Oct 2019 05:56:50 +0000'
    ]
];
```

### Feito isso vá até seu banco e altere sua url base
```
Procure pela tabela: core_config_data
Procure pelo campo: path - web/unsecure/base_url
altere o campo value nessa mesma para a URL que você configurou
```

Uma vez feito isso, limpe o cache da aplicação:
Rode os seguintes comandos

    bin/magento deploy:mode:set production
    bin/magento cache:flush
    bin/magento deploy:mode:set developer
    bin/magento cache:flush

  

## Tudo certo?

Acesse o endereço da sua aplicação, a tela é para estar parecida com essa aqui:
![home](https://github.com/olucasu/magento2/blob/master/screenshots/home.png)


### Acesso ao admin
SUA_URL/admin_8xq1vd


User: **admin** 
Password : **admin123**
