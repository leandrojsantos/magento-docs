## Renderização de modelo e layout

Essa é arquitetura de pasta para um modulo, para o registro usar cmd

    bin/magento setup:upgradecomando 

![modulo3](https://user-images.githubusercontent.com/26981092/78250088-b870c900-74c5-11ea-8924-8bd2fe34b244.png)

obs  magento2 nao necessario que seguir essa arquitetura poucos sao diretorios fixos (etc/module.xml) devido a namespaces, veja um exemplo de modulo 

 ![modulo](https://user-images.githubusercontent.com/26981092/78152548-573aee00-7410-11ea-9c47-b4c849b3072a.png) 


## O registration.phparquivo:

    <?php

    use Magento\Framework\Component\ComponentRegistrar;

    ComponentRegistrar::register(
        ComponentRegistrar::MODULE,
        'MageMastery_FirstLayout',
        __DIR__
    );
    O module.xmlarquivo:

    <?xml version="1.0"?>
    <config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">
        <module name="MageMastery_FirstLayout" />
    </config>


Além disso, adicionei uma route.xml arquivo com uma classe do Action Controller. 

Esses dois arquivos permitem acessar uma página personalizada que foi declarada no MageMastery_FirstLayout módulo.

O routes.xml conteúdo do arquivo é o seguinte:

    <?xml version="1.0"?>
    <config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xsi:noNamespaceSchemaLocation="urn:magento:framework:App/etc/routes.xsd">
        <router id="standard">
            <route id="magemastery_firstlayout" frontName="firstlayout">
                <module name="MageMastery_FirstLayout" />
            </route>
        </router>
    </config>

O arquivo routes.xml está localizado no MageMastery/FirstLayout/etc/frontend diretório Essa configuração permite acessar o controlador de um módulo usando o caminho de URI "firstlayout" da URL.

E a classe Action Controller localizada no MageMastery/FirstLayout/Controller/Page/View.php arquivo, o conteúdo é:

    <?php

    declare(strict_types=1);

    namespace MageMastery\FirstLayout\Controller\Page;

    use Magento\Framework\App\Action\Action;
    use Magento\Framework\Controller\ResultFactory;
    use Magento\Framework\View\Result\Page;

    class View extends Action
    {
        public function execute()
        {
            /** @var Page $resultPage */
            return $this->resultFactory->create(ResultFactory::TYPE_PAGE);
        }
    }

Como você notou, há uma instância da Magento\Framework\View\Result\Pageclasse retornada do execute()método.

    return $this->resultFactory->create(ResultFactory::TYPE_PAGE);

A idéia por trás da criação de um TYPE_PAGEtipo de objeto de resultado é acionar o mecanismo de renderização do Magento 2.

## Renderização

O frontend e o admin html são as duas áreas que podemos usar para renderizar um conteúdo personalizado nas áreas Store front e Admin.

Áreas de renderização de um módulo no Magento 2

Existem outros arquivos e diretórios que podem estar localizados nos view diretórios

frontend Diretório interno , existem dois diretórios. O layoutdiretório é usado para colocar os arquivos de configuração de layout do MageMastery_FirstLayoutmódulo.

O templates  diretório contém modelos PHTML que precisam ser renderizados em uma loja Magento2.

A estrutura do diretório view

![modulo2](https://user-images.githubusercontent.com/26981092/78152818-b7ca2b00-7410-11ea-9ece-983895bec518.png)

Arquivo de configuração de layout
O novo

magemastery_firstlayout_page_view.xmlarquivo de configuração de layout deve ser adicionado ao MageMastery/FirstLayout/view/frontend/layout  diretório

    <?xml version="1.0"?>
    <page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
        <body>
            <referenceContainer name="content">
                <block class="Magento\Framework\View\Element\Template"
                      name="magemastery.first.layout.example"
                      template="MageMastery_FirstLayout::example.phtml" />
            </referenceContainer>
        </body>
    </page>

O formato do arquivo de layout é bastante semelhante a todos os arquivos XML no Magento2. 
O nó raiz <page /> permite adicionar configurações e modelos que precisam ser renderizados em uma página.

Para adicionar um example.phtml modelo personalizado do view/frontend/templates diretório, a declaração XML deve ser criada e adicionada ao arquivo de configuração do layout.

O caminho para um modelo. Geralmente inclui o nome de um módulo associado ao nome do arquivo de modelo "::". 

Como você pode notar, também não há um templates diretório especificado para o arquivo 

    template="MageMastery_FirstLayout::example.phtml". 

O conteúdo do example.phtml arquivo é renderizado pela

    Magento\Framework\View\Element\Templateclasse.

Nome do arquivo de layout
Vamos dar uma olhada no nome do arquivo de layout. 

Primeiro de tudo, um nome do arquivo de layout deve incluir o ID da rota. 

O ID da rota está localizado no etc/frontend/routes.xml arquivo, este é o magemastery_firstlayoutID. 

A page parte do nome do layout vem de um nome do diretório do controlador. 