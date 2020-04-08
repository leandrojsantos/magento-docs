# Instalação do magento2 usando img docker lemp + xdebug

* Cadastro no site do magento para criar acess keys, no marktingplace

* Sera necessário o uso básico do docker... 
caso não tenha segue link: 

https://github.com/leandrojsantos/docker

Ápos escolher o diretorio e usa docker compose e necessário entra no cotainer do php73 feito isso

O seguinte comando para começar com a instalação do compositor.


    composer create-project --repository=https://repo.magento.com/ magento/project-community-edition

Navegue para o diretório project-community-edition e observe os arquivos instalados.

    cd project-community-edition/
    ls -la .

Definindo permissões de arquivo para o projeto Open Source do Magento2, precisamos definir permissões de arquivo apropriadas. 

A página Instalação do Magento fornece um conjunto de comandos que precisamos executar para definir permissões para arquivos e diretórios de um projeto de código aberto do Magento2.

    find var generated vendor pub/static pub/media app/etc -type f -exec chmod g+w {} +
    find var generated vendor pub/static pub/media app/etc -type d -exec chmod g+ws {} +
    chmod u+x bin/magento

Você também pode precisar executar mais um comando no diretório do projeto Magento se o usuário do servidor da web estiver configurado para executar como parte de um grupo www-data .

    chown -R :www-data .

Linha de comando

    bin/magento setup:install \
    --base-url=http://magento2ce.nomedalojacom \
    --db-host=localhost \
    --db-name=magento \
    --db-user=magento \
    --db-password=magento \
    --admin-firstname=admin \
    --admin-lastname=admin \
    --admin-email=nomedoadmin@admin.com \
    --admin-user=admin \
    --admin-password=admin123 \
    --language=en_US \
    --currency=USD \
    --timezone=America/Chicago \
    --use-rewrites=1