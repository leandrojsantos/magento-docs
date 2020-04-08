# Estrutura inicial 

Arquitetural do Magento, para implementar interfaces de usuário. 
Os diagramas mostram a arquitetura do Magento que e baseada em mvc

![arquitetura-magento1](https://user-images.githubusercontent.com/26981092/78785367-6de7c480-797d-11ea-90e1-0e5d1d38eaa7.jpg)

![mvc-magento1](https://user-images.githubusercontent.com/26981092/78785846-2f9ed500-797e-11ea-919f-3b1263663b79.png)


>User Request - O usuário envia uma solicitação para um servidor na forma de mensagem de solicitação em que navegadores da web, mecanismos de pesquisa etc. agem como clientes.

>View - Ver representa os dados em um formato específico. É a interface do usuário responsável por exibir a resposta para a solicitação do usuário. Ele especifica uma ideia por trás da apresentação dos dados do modelo ao usuário. As visualizações são usadas para refletir "como devem ser os seus dados".

>Controller - O controlador é responsável por responder à entrada do usuário e executar interações nos objetos do modelo de dados. Ele usa modelos para processar os dados e enviar respostas de volta à exibição.

>Model - O modelo é responsável por gerenciar os dados do aplicativo. Ele contém a lógica dos dados e representa o objeto de dados básico na estrutura. Ele responde ao pedido da visualização e às instruções do controlador para se atualizar.

>DataBase - O banco de dados contém as informações solicitadas ao usuário. Quando o usuário solicita dados, o view envia solicitações ao controlador, o controlador solicita ao modelo e o modelo busca as informações necessárias no banco de dados e responde ao usuário.

>WSDL - WSDL significa Linguagem de Descrição de Serviços da Web. É usado para descrever serviços da Web e como acessá-los.

# mvc

* Código Organizado em Módulos
Magento organiza seu código em módulos individuais. Em uma aplicação típica MVC (Model Model View Controller) , todos os controladores estarão em uma pasta, todos os modelos em outra etc. No Magento, os arquivos são agrupados com base na funcionalidade, denominada módulos no Magento.

* Código Magento
Por exemplo, você encontrará Controladores, Modelos, Auxiliares, Blocos etc. relacionados à funcionalidade de checkout do Magento em

        app / code / core / Mage / Checkout

* Outro ex você na pasta encontrará Controladores, Modelos, Auxiliares, Blocos etc. relacionados à funcionalidade do Google Checkout do Magento em

        app / code / core / Mage / GoogleCheckout

* Seu código
Quando você deseja personalizar ou estender o Magento, em vez de editar diretamente os arquivos principais, ou mesmo colocar seus novos Controladores, Modelos, Auxiliares, Blocos etc. ao lado do código Magento, você criará seus próprios Módulos.

        app / code / local / Package / Modulename

* Package (também conhecido como espaço para nome ) é um nome exclusivo que identifica sua empresa ou organização. A intenção é que cada membro da comunidade mundial do Magento use seu próprio nome de pacote ao criar módulos para evitar colidir com o código de outro usuário. Ao criar um novo módulo, você precisa contar ao Magento. Isso é feito adicionando um arquivo XML à pasta:

        app / etc / modules

Existem dois tipos de arquivos nesta pasta, o primeiro habilita um módulo individual e é nomeado no formato: Packagename_Modulename.xml

O segundo é um arquivo que habilitará vários módulos a partir de um pacote / espaço para nome e é nomeado no formato: Packagename_All.xml . Observe que ele é usado apenas pela equipe principal com o arquivo Mage_All.xml . Não é recomendável ativar vários módulos em um único arquivo, pois isso quebra a modularidade de seus módulos.