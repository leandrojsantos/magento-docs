# Instalação do magento2 usando img docker lemp + xdebug

Sera necessario uso do docker...

Meta-pacote Magento

O seguinte comando para começar com a instalação do compositor.

Código aberto Magento 2

    composer create-project --repository=https://repo.magento.com/ magento/project-community-edition


Navegue até a raiz do documento do servidor da web e cole o comando de instalação na linha de comandos.

Este comando procurará a edição mais recente do Magento2 Open Source dentro do repositório que especificamos no parâmetro --repository . 

No meu caso, é o Magento Open Source versão 2.3.3. O Composer criará um diretório project-community-edition e extrairá nele os arquivos do projeto Magento.

Pode demorar alguns minutos para baixar todas as dependências do projeto Magento2 Open Source.

Como resultado, temos um novo diretório project-community-edition criado onde todos os arquivos do projeto Magento estão localizados.

Navegue para o diretório project-community-edition e observe os arquivos instalados.

    cd project-community-edition/
    ls -la .

Definindo permissões de arquivo
Para o projeto Open Source do Magento2, precisamos definir permissões de arquivo apropriadas. 

A página Instalação do Magento fornece um conjunto de comandos que precisamos executar para definir permissões para arquivos e diretórios de um projeto de código aberto do Magento2.

    find var generated vendor pub/static pub/media app/etc -type f -exec chmod g+w {} +
    find var generated vendor pub/static pub/media app/etc -type d -exec chmod g+ws {} +
    chmod u+x bin/magento

Você também pode precisar executar mais um comando no diretório do projeto Magento se o usuário do servidor da web estiver configurado para executar como parte de um grupo www-data .

    chown -R :www-data .

Linha de comando

    bin/magento setup:install \
    --base-url=http://magento2ce.com \
    --db-host=localhost \
    --db-name=magento \
    --db-user=magento \
    --db-password=magento \
    --admin-firstname=admin \
    --admin-lastname=admin \
    --admin-email=admin@admin.com \
    --admin-user=admin \
    --admin-password=admin123 \
    --language=en_US \
    --currency=USD \
    --timezone=America/Chicago \
    --use-rewrites=1