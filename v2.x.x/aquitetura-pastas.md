# Estrutura inicial magento2

Ao criar um novo projeto se cria essa estrutura de pastas onde :

![pastas](https://user-images.githubusercontent.com/26981092/78052846-141c4480-7356-11ea-9fab-9c0f193373b9.png)

* O diretório APP

é responsável por armazenar todos os arquivos de configuração, quaisquer temas instalados em um aplicativo Magento 2 Open Source,ou seja, aqui fica  modulos criados pelo time (local host) os de terceios fica no diretorio vander

Os arquivos de temas incluem PHTML, HTML, CSS, LESS, JavaScript e imagens.

Além disso, dentro do app diretório, você pode encontrar arquivos de código fonte. 

* O diretório BIN

O bin diretório fornece uma ferramenta Magento CLI (Command Line Interface), ou seja, aqr binarios. 

Um exemplo desse comando pode ser um cache limpo ou compilação. 

* O diretório DEV

O Magento 2 inclui 8 tipos diferentes de testes dentro do dev/tests diretório. 

Cada diretório inclui testes de um tipo específico e código adicional que ajuda a executar testes.

* O diretório GENERATED

Este diretório contém todas as classes PHP geradas automaticamente. 

Depende do modo que esta produção ou desenvolvimento 

* O diretório LIB

O diretório inclui internale e web diretórios.

E o web diretório fornece arquivos de interface ou da web relacionados. 

Inclui bibliotecas de terceiros JavaScript, como jQuery, KnockoutJS, RequireJS e outras bibliotecas. 

* O diretório PHPSERVER

Ele fornece um servidor PHP simples que pode ser usado para desenvolvimento. 

Não é recomendável usar o router, phpcomo parte da configuração de produção.

* O diretório PUB

O index.php arquivo principal é responsável pelo processamento de solicitações HTTP. 

Há um static.php arquivo responsável pelo processamento de todos os arquivos JavaScript, CSS e HTML e pela localização dos arquivos no pub/static diretório

O diretório de instalação
Este diretório é usado para fornecer um Assistente de Configuração da Web e outros scripts relacionados à instalação, ou seja, so deve ser usado em modo de produção

* Diretório SETUP

É uma segunda maneira de como você pode instalar o Magento2

Open Source, além da instalação da linha de comando.

* O diretório VAR

Você pode encontrar o cache do aplicativo Magento2 e do diretório da sessão. 

Caso sua configuração diga que todas as sessões precisam ser armazenadas em um sistema de arquivos, o var/session diretório conterá todos os arquivos de sessão do usuário.

Além disso, o var/log diretório em que todos os erros, exceções do PHP, serão criados e armazenados.

* O diretório VENDER

Este diretório inclui todas as dependências de terceiros instaladas (compose). 

Ele também inclui arquivos de origem Magento2 no vendor/magento diretório 

* Outros arquivos

O Magento2 Open Source fornece diferentes arquivos de configuração de amostra para o servidor Apache e o Nginx. 

Por exemplo grunt-config.json.sample, arquivos Gruntfile.js.samplee packages.json é responsável por instalar dependências de frontend / JavaScript.

O último é o composer.json arquivo. Ele fornece todas as dependências no aplicativo Magento2. 

Se queremos instalar o módulo Magento2 adicional ou testar o aplicativo. Durante o desenvolvimento, esse arquivo será usado com freqüência.