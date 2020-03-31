# Estrutura inicial 

Ao criar um novo projeto se cria essa estrutura de pastas onde :

![pastas](https://user-images.githubusercontent.com/26981092/78052846-141c4480-7356-11ea-9fab-9c0f193373b9.png)



>O diretório app

    é responsável por armazenar todos os arquivos de configuração, quaisquer temas instalados em um aplicativo Magento 2 Open Source.

    Os arquivos de temas incluem PHTML, HTML, CSS, LESS, JavaScript e imagens. Além disso, dentro do app diretório, você pode encontrar arquivos de código fonte. 

    No momento, não há arquivos no code diretório em uma instalação vazia do Magento 2 Open Source. Geralmente, todas as personalizações adicionais acontecem dentro do app/code diretório.



>O diretório bin

    O bindiretório fornece uma ferramenta Magento CLI (Command Line Interface). 

    Ele fornece uma interface via CLI para diferentes comandos do Magento 2 para realizar alterações no aplicativo Magento 2. 

    Um exemplo desse comando pode ser um cache limpo ou compilação. 

>O diretório dev

    O devdiretório contém todos os testes que acompanham o Magento 2 Open Source.

    O Magento 2 inclui 8 tipos diferentes de testes dentro do dev/testsdiretório. 

    Cada diretório inclui testes de um tipo específico e código adicional que ajuda a executar testes.


>O dev/toolsdiretório 

    fornece ferramentas diferentes e sua configuração, como o Grunt e outros arquivos não utilizados com frequência.

>O diretório generated

    Este diretório contém todas as classes PHP geradas automaticamente. 

    O Magento 2 depende da geração de código PHP. De tempos em tempos, examinaremos o generated diretório para entender a lógica usada para executar uma determinada operação.

>O diretório lib

    O diretório inclui internale e web diretórios.

    O internal diretório não é algo com o qual vamos trabalhar. 

    E o web diretório fornece arquivos de interface ou da web relacionados. Inclui bibliotecas de terceiros JavaScript, como jQuery, KnockoutJS, RequireJS e outras bibliotecas. Também web inclui arquivos CSS / LESS, bem como documentação da interface do usuário.

>O diretório phpserver

    Ele fornece um servidor PHP simples que pode ser usado para desenvolvimento. 

    Não é recomendável usar o router, phpcomo parte da configuração de produção de código aberto do Magento 2.

>O diretório pub

    Este é um diretório raiz do servidor de um aplicativo Magento 2. O index.php arquivo principal é responsável pelo processamento de solicitações HTTP. 

    Há um static.phparquivo responsável pelo processamento de todos os arquivos JavaScript, CSS e HTML e pela localização dos arquivos no pub/static diretório

    Todo o conteúdo da mídia que será carregado, seja via Magento Admin ou algum programaticamente, é armazenado no pub/media diretório.

    O diretório de instalação
    Este diretório é usado para fornecer um Assistente de Configuração da Web e outros scripts relacionados à instalação, ou seja, so deve ser usado em modo de produção

>Diretório setup

    É uma segunda maneira de como você pode instalar o Magento 2 Open Source, além da instalação da linha de comando.

>O diretório var

    Você pode encontrar o cache do aplicativo Magento 2 e do diretório da sessão. 

    Caso sua configuração diga que todas as sessões precisam ser armazenadas em um sistema de arquivos, o var/session diretório conterá todos os arquivos de sessão do usuário.

    Além disso, o var/log diretório em que todos os erros, exceções do PHP, serão criados e armazenados.

>O diretório vender

    Este diretório inclui todas as dependências de terceiros instaladas. 

    Ele também inclui arquivos de origem Magento 2 no vendor/magentodiretório Vamos examinar o diretório com bastante frequência durante o desenvolvimento.

>Outros arquivos

    O Magento 2 Open Source fornece diferentes arquivos de configuração de amostra para o servidor Apache e o Nginx. 

    Por exemplo grunt-config.json.sample, arquivos Gruntfile.js.samplee packages.jsoné responsável por instalar dependências de frontend / JavaScript.

    O último é o composer.jsonarquivo. Ele fornece todas as dependências no aplicativo Magento 2. Se queremos instalar o módulo Magento 2 adicional ou testar o aplicativo. Durante o desenvolvimento, esse arquivo será usado com freqüência.