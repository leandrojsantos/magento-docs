# MVC baseado em configuração

![mvc2-v1](https://user-images.githubusercontent.com/26981092/78821377-c6d05080-79af-11ea-9c90-2f711cb4ec1b.png)

Magento é um sistema MVC baseado em configuração . A alternativa para isso seria um sistema MVC baseado em convenção .

Em um sistema MVC baseado em convenções, se você quiser adicionar, digamos, um novo Controller ou talvez um novo Modelo, basta criar o arquivo / classe e o sistema o buscará automaticamente.

Em um sistema baseado em configuração, como o Magento, além de adicionar o novo arquivo / classe à base de código, muitas vezes você precisa informar explicitamente ao sistema sobre a nova classe ou o novo grupo de classes. 

No Magento, cada módulo possui um arquivo chamado config.xml, este arquivo contém toda a configuração relevante para um módulo Magento. No tempo de execução, todos esses arquivos são carregados em uma grande árvore de configuração.

Por exemplo, deseja usar modelos em seu módulo personalizado? Você precisará adicionar um código ao config.xml que informe ao Magento que você deseja usar o Models, bem como qual deve ser o nome da classe base de todos os seus modelos.

    <models>
         <packagename>
              <class>Packagename_Modulename_Model</class>
        </packagename>
    </models>

O mesmo vale para auxiliares, blocos, rotas para seus controladores, manipuladores de eventos e muito mais. 

Quase sempre que você quiser explorar o poder do sistema Magento, precisará fazer alguma alteração ou adição ao seu arquivo de configuração.

>Controladores

Em qualquer sistema PHP, o principal ponto de entrada do PHP continua sendo um arquivo PHP. Magento não é diferente, e esse arquivo é index.php .

No entanto, você nunca CODE no index.php. Em um sistema MVC, o index.php conterá código / chamadas para o código que faz o seguinte:

* Examina a URL

* Com base em algum conjunto de regras, transforma esse URL em uma classe Controller e em um método Action (chamado Routing)

* Instancia a classe Controller e chama o método Action (chamado dispatching)

Isso significa que o ponto de entrada prático no Magento (ou qualquer sistema baseado em MVC) é um método em um arquivo do Controller. Considere o seguinte URL:

    http://example.com/catalog/category/view/id/25

Cada parte do caminho após o nome do servidor é analisada da seguinte maneira.

* Front Name -> A primeira parte do URL é chamada de Front Name. Isto, mais ou menos, diz ao magento em qual módulo ele pode encontrar um controlador. 

No exemplo acima, o Front Name é catalog, que corresponde ao módulo localizado em:

        app / code / core / Mage / Catalog

* Controller Name -> A segunda parte da URL diz ao Magento qual controlador ele deve usar. 

Cada módulo com controladores possui uma pasta especial denominada 'controladores', que contém todos os controladores de um módulo. 

No exemplo acima, a categoria de parte da URL é convertida no arquivo Controller

        app / code / core / Mage / Catalog / controllers / CategoryController.php

Que parece

        classe Mage_Catalog_CategoryController estende Mage_Core_Controller_Front_Action
        {
        }

Todos os controladores no aplicativo de carrinho Magento se estendem de Mage_Core_Controller_Front_Action.

* Action Name -> Terceiro em nosso URL é o Action Name. No nosso exemplo, isso é "view". 

A palavra "visualização" é usada para criar o método de ação. Portanto, em nosso exemplo, "view" seria transformado em "viewAction"

        class Mage_Catalog_CategoryController extends Mage_Core_Controller_Front_Action
        {
            public function viewAction()
            {
                //main entry point
            }
        }

*  Parameter/Value - id/25 -> Quaisquer partes do caminho após o Action Name serão consideradas variáveis ​​de solicitação GET de chave / valor. 

Portanto, em nosso exemplo, o "id / 25" significa que haverá uma variável GET chamada "id", com o valor "25".

Como mencionado anteriormente, se você quiser que seu módulo use controladores, precisará configurá-los. 

Abaixo está o pedaço de configuração que permite controladores para o módulo de catalog

    <frontend>
        <routers>
            <catalog>
                <use> padrão </use>
                <args>
                    <module> Mage_Catalog </module>
                    <frontName> catalog </frontName>
                </args>
            </catalog>
        </routers>
    </frontend>

Não se preocupe muito com os detalhes no momento, mas observe o <frontName> catalog </frontName>

É isso que vincula um módulo a um Front Name da URL. A maioria dos módulos principais do Magento escolhe um nome de frente igual ao nome do módulo, mas isso não é necessário.

* Multiple Routers -> O roteamento descrito acima é para o aplicativo de carrinho Magento (geralmente chamado de front-end). 

Se o Magento não encontrar um Controlador / Ação válido para um URL, ele tenta novamente, desta vez usando um segundo conjunto de regras de Roteamento para o aplicativo Admin. 

E se ainda não encontrar um Admin Controller / Action válido , ele usa um Controller especial chamado Mage_Cms_IndexController .

O CMS Controller verifica o sistema de gerenciamento de conteúdo do Magento para ver se há algum conteúdo que deve ser carregado. Se encontrar algum, carrega-o, caso contrário, o usuário receberá uma página 404.

Por exemplo, a página principal de "índice" do magento é aquela que usa o CMS Controller, que geralmente pode lançar novos usuários para um loop.

# Carregamento de modelo de URI baseado em contexto

Agora que estamos no ponto de entrada do método Action, queremos começar a instanciar classes que fazem coisas. 

O Magento oferece uma maneira especial de instanciar modelos, auxiliares e blocos usando métodos estáticos de fábrica na classe Mage global. Por exemplo:

    Mage :: getModel ('catalog / product');
    Mage :: helper ('catalog / product');

A sequência 'catalog / product' é chamada de Nome da Classe Agrupada. Também é frequentemente chamado de URI. 

A primeira parte de qualquer Nome de Classe Agrupada (neste caso, catalog) é usada para procurar em qual Módulo a classe reside. 

A segunda parte ('product' acima) é usada para determinar qual classe deve ser carregada.

Portanto, nos dois exemplos acima, o 'catalog' é resolvido para o módulo 

    app / code / core / Mage / Catalog .

Ou seja, o nome da nossa classe começará com Mage_Catalog .

Em seguida, o product é adicionado para obter o nome da classe final

    Mage :: getModel ('catalog / product');
    Mage_Catalog_Model_Product

    Mage :: helper ('catalog / product');
    Mage_Catalog_Helper_Product

Essas regras estão vinculadas ao que foi configurado no arquivo de configuração de cada módulo. 

Ao criar seu próprio módulo personalizado, você terá seus próprios nomes de classe agrupados (também chamados de grupos de classes) para trabalhar como 
    
    Mage :: getModel ('myspecialprefix / modelname'); .

Você não precisa usar nomes de classes agrupadas para instanciar suas classes. No entanto, como aprenderemos mais adiante, há algumas vantagens em fazer isso.

# Magento Models

O Magento, como a maioria das estruturas atualmente, oferece um sistema ORM (Object Relational Mapping). 

Os ORMs tiram você do negócio de escrever SQL e permitem que você manipule um armazenamento de dados apenas através do código PHP. Por exemplo:

    $model = Mage::getModel('catalog/product')->load(27);
    $price = $model->getPrice();
    $price += 5;
    $model->setPrice($price)->setSku('SK83293432');
    $model->save();

No exemplo acima, chamamos os métodos "getPrice" e "setPrice" em nosso product. 

No entanto, a classe Mage_Catalog_Model_Product não possui métodos com esses nomes. Isso ocorre porque o ORM do Magento usa o método magic __call do PHP para implementar getters e setters.

Calling the method $product-> getPrice (); 
"obterá" o atributo "price" do modelo.

Calling $product-> setPrice (); "definirá" o atributo "price" do modelo. Tudo isso pressupõe que a classe Model ainda não possua métodos denominados getPrice ou setPrice. 

Se isso acontecer, os métodos mágicos serão ignorados. Se você estiver interessado na implementação disso, verifique a classe Varien_Object, da qual todos os modelos herdam.

Se você deseja obter todos os dados disponíveis em um modelo, call $product-> getData (); para obter uma matriz de todos os atributos.

Você também notará que é possível encadear várias chamadas para o método set:

    $model-> setPrice ($price) -> setSku ('SK83293432');

Isso ocorre porque cada método set retorna uma instância do Model. 

O ORM do Magento também contém uma maneira de consultar vários objetos através de uma interface de coleções. A seguir, obteríamos uma coleção de todos os products que custam R$5,00

    $products_collection = Mage :: getModel ('catalog / product')
    -> getCollection ()
    -> addAttributeToSelect ('*')
    -> addFieldToFilter ('price', '5,00');

Novamente, você notará que o Magento implementou uma interface de encadeamento aqui. 

As coleções usam a Biblioteca Padrão do PHP para implementar objetos que possuem propriedades de matriz.

    foreach($products_collection as $product)
    {
        echo $product->getName();
    }


# Helpers

As classes Helper do Magento contêm métodos utilitários que permitem executar tarefas comuns em objetos e variáveis. ex

    $helper = Mage :: helper ('catalog');

Você notará que deixamos de lado a segunda parte do nome da classe agrupada. 

Cada módulo possui uma classe auxiliar de dados padrão. O seguinte é equivalente ao acima:

    $helper = Mage :: helper ('catalog / data');

A maioria dos Helpers herda o formulário Mage_Core_Helper_Abstract, que fornece vários métodos úteis por padrão.

    $translate_output = $helper -> __ ('Magento é ótimo'); // traduções do estilo gettext
    if ($helper-> isModuleOutputEnabled ()): // está ativado ou desativado este módulo?


# Layouts

Em um sistema PHP MVC típico, depois de manipularmos nossos modelos,

* Defina algumas variáveis ​​para nossa visão

* O sistema carregaria um layout HTML "externo" padrão>

* O sistema carregaria nossa visão dentro desse layout externo

No entanto, se você observar uma ação típica do Magento Controller, não verá nada disso:

    /**
    * View product gallery action
    */
    public function galleryAction()
    {
        if (!$this->_initProduct()) {
            if (isset($_GET['store']) && !$this->getResponse()->isRedirect()) {
                $this->_redirect('');
            } elseif (!$this->getResponse()->isRedirect()) {
                $this->_forward('noRoute');
            }
            return;
        }
        $this->loadLayout();
        $this->renderLayout();
    }

Em vez disso, a ação do controlador termina com duas chamadas

    $this-> loadLayout ();
    $this-> renderLayout ();

Portanto, o "V" no MVC do Magento já difere do que você provavelmente está acostumado, pois precisa explicitamente iniciar a renderização do layout.

O layout em si também difere. Um layout Magento é um objeto que contém uma coleção aninhada / em árvore de objetos "Bloquear". 

Cada objeto Block renderiza um bit específico de HTML. Objetos de bloco fazem isso através de uma combinação de código PHP e incluindo arquivos de modelo .phtml do PHP.

Objetos de blocos devem interagir com o sistema Magento para recuperar dados de modelos, enquanto os arquivos de modelo phtml produzirão o HTML necessário para uma página.

    Por exemplo, o cabeçalho da página 

    Block app / code / core / Mage / Page / Block / Html / Head.php 

    usa a página do arquivo head.phtml / html / head.phtml .

Outra maneira de pensar sobre isso é que as classes Block são quase como mini-controladores, e os arquivos .phtml são a visão.

Por padrão, quando você chama

    $this-> loadLayout ();
    $this-> renderLayout ();

O Magento carregará um Layout com uma estrutura de site esqueleto. Haverá blocos de estrutura para fornecer seu html , cabeçalho e corpo , além de HTML para configurar uma ou várias colunas do layout. 

Além disso, haverá alguns Blocos de conteúdo para navegação, mensagem de boas-vindas padrão etc.

"Estrutura" e "Conteúdo" são designações arbitrárias no sistema Layout. 

Um bloco não sabe programaticamente se é estrutura ou conteúdo, mas é útil pensar em um bloco como um ou outro.

Para adicionar conteúdo a este layout, você precisa dizer ao sistema Magento uma ação do controlador

public function indexAction()
{
    $this->loadLayout();
    $block = $this->getLayout()->createBlock('adminhtml/system_account_edit')
    $this->getLayout()->getBlock('content')->append($block);
    $this->renderLayout();
}

mas mais comumente (pelo menos no aplicativo de carrinho de front-end), é o uso do sistema XML Layout.

Os arquivos XML de layout em um tema permitem que você, por Controlador, remova blocos que normalmente seriam renderizados ou adicione blocos a essas áreas de esqueleto padrão. Por exemplo, considere este arquivo XML de layout:

    <catalog_category_default>
        <reference name="left">
            <block type="catalog/navigation" name="catalog.leftnav" after="currency" template="catalog/navigation/left.phtml"/>
        </reference>
    </catalog_category_default>

Está dizendo no módulo catalog, na categoria Controlador, e na Ação padrão, insira o Bloco de catalog / navegação no bloco de estrutura "esquerda", usando o modelo de catalog / navegação / left.phtml.

Uma última coisa importante sobre Blocks. 

Você verá frequentemente o código em modelos mais ou menos assim:

    $this-> getChildHtml ('order_items')

É assim que um bloco renderiza um bloco aninhado. No entanto, um bloco pode renderizar um bloco filho apenas se o bloco filho for incluído como um bloco aninhado no arquivo XML de layout . 

No exemplo acima, nosso bloco de catalog / navegação não possui blocos aninhados. Isso significa que qualquer chamada para $this-> getChildHtml () no left.phtml será renderizada em branco.

Se, no entanto, tivéssemos algo como:

    <catalog_category_default>
        <reference name="left">
            <block type="catalog/navigation" name="catalog.leftnav" after="currency" template="catalog/navigation/left.phtml">
                <block type="core/template" name="foobar" template="foo/baz/bar.phtml"/>
            </block>
        </reference>
    </catalog_category_default>

No bloco de catálogo / navegação, poderíamos chamar
$this-> getChildHtml ('foobar');

# Observers

Observer para que os usuários finais se conectem. À medida que certas ações acontecem durante uma solicitação de Página (um Modelo é salvo, um usuário efetua login etc.), o Magento emitirá um sinal de evento.

Ao criar seus próprios módulos, você pode "ouvir" esses eventos.

Digamos que você queira receber um email sempre que um determinado cliente fizer login na loja. Você pode ouvir o evento "customer_login" (configuração em config.xml)

    <events>
        <customer_login>
            <observers>
                <unique_name>
                    <type>singleton</type>
                    <class>mymodule/observer</class>
                    <method>iSpyWithMyLittleEye</method>
                </unique_name>
            </observers>
        </customer_login>
    </events>

e, em seguida, escreva um código que seja executado sempre que um usuário fizer login:

    class Packagename_Mymodule_Model_Observer
    {
        public function iSpyWithMyLittleEye($observer)
        {
            $data = $observer->getData();
            //code to check observer data for our user,
            //and take some action goes here
        }
    }