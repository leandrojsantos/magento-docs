# Magento Layouts, Blocks e Templates

Diferente de muitos sistemas MVC populares, o Action Controller do Magento não passa um objeto de dados para a visualização nem configura propriedades no objeto de visualização (com algumas exceções). 

Em vez disso, o componente View faz referência diretamente aos templates de sistema para obter as informações necessárias para exibição.

Uma conseqüência dessa decisão de design é que a Visualização foi separada em block e templates.

block são objetos PHP, templates são arquivos PHP "brutos" (com extensão .phtml) que contêm uma mistura de HTML e PHP (onde o PHP é usado como uma linguagem de modelagem).

Cada block está vinculado a um único arquivo de template. 

Dentro de um arquivo phtml, a palavra 

- chave $ this do PHP conterá uma referência ao objeto Block do template. Um exemplo template defaulfdo produto no arquivo em

app / design / frontend / base / defaulf/ template / catálogo / produto / list.phtml


    <?php $_productCollection=$this->getLoadedProductCollection() ?>
    <?php if(!$_productCollection->count()): ?> 
    
      <div class="note-msg">
        <?php echo $this->__("There are no products matching the selection.") ?>
      </div> <?php else: ?>
    ...

O método getLoadedProductCollection pode ser encontrado na classe Block do template, Mage_Catalog_Block_Product_List , conforme mostrado:

    File: app/code/core/Mage/Catalog/Block/Product/List.php
    ...
    public function getLoadedProductCollection()
    {
        return $this->_getProductCollection();
    }
    ...

_GetProductCollection do block instancia templates e lê seus dados, retornando um resultado ao template.

# Nesting Blocks

O verdadeiro poder dos block / templates vem com o método getChildHtml . 

Isso permite incluir o conteúdo de um block / template secundário dentro de um block / template primário.

block chamando block chamando block é como todo o layout HTML da sua página é criado. 

Dê uma olhada no template de layout de uma coluna.

    File: app/design/frontend/base/default/template/page/1column.phtml
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
    <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="<?php echo $this->getLang() ?>" lang="<?php echo $this->getLang() ?>">
    <head>
    <?php echo $this->getChildHtml('head') ?>
    </head>
    <body class="page-popup <?php echo $this->getBodyClass()?$this->getBodyClass():'' ?>">
        <?php echo $this->getChildHtml('content') ?>
        <?php echo $this->getChildHtml('before_body_end') ?>
        <?php echo $this->getAbsoluteFooter() ?>
    </body>

O template em si tem apenas 28 linhas. No entanto, cada chamada para $ this-> getChildHtml (...) incluirá e renderizará outro block. 

Esses blocos, por sua vez, usarão getChildHtml para renderizar outros blocos. São blocos todo o caminho.

# O layout

O objeto de layout é um objeto XML que definirá quais blocos serão incluídos em uma página e quais blocos devem iniciar o processo de renderização.

Na última vez , estávamos ecoando o conteúdo diretamente de nossos Métodos de action. 

Desta vez, vamos criar um template HTML simples para o nosso módulo Hello World.

Primeiro, crie um arquivo em

app / design / frontend / base / defaulf/ layout / local.xml

com o seguinte conteúdo

    <layout version="0.1.0">
        <default>
            <block type="page/html" name="root" output="toHtml" template="magentotutorial/helloworld/simple_page.phtml" />
        </default>
    </layout>

Em seguida, crie um arquivo em

app / design / frontend / base / defaulf/ template / magentotutorial / helloworld / simple_page.phtml

com o seguinte conteúdo

    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"
            "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Hello World</title>
        <style type="text/css">
            body {
                background-color:#f00;
            }
        </style>
    </head>
    <body>

    </body>
    </html>

Finalmente, cada controlador de action é responsável por iniciar o processo de layout.

Precisamos adicionar duas chamadas de método ao método de action.

    public function indexAction() {
        //remove our previous echo
        //echo 'Hello Index!';
        $this->loadLayout();
        $this->renderLayout();
    }

Depois de instalar o módulo (semelhante ao modo como você configura o módulo Configviewer ), acesse o seguinte URL

http://example.com/helloworld/index/index?showLayout=page

Este é o xml de layout para sua página / solicitação. É composto pelas tags <block />, <reference /> e <remove />.

Quando você chama o método loadLayout do seu Action Controller, o Magento irá

Gere este XML de layout

Instancie uma classe Block para cada tag <block />, procurando a classe usando o atributo type da tag como um caminho de configuração global e armazene-a na matriz _blocks interna do objeto de layout, usando o atributo name da tag como a chave da matriz.

Se a tag <block /> contiver um atributo de saída, seu valor será adicionado à matriz _output interna do objeto de layout.

Então, quando você chama o método renderLayout no seu Action Controller, o Magento irá percorrer todos os blocos na matriz _output , usando o valor do atributo de saída como método de retorno de chamada. 

Isso sempre é toHtml , e significa que o ponto de partida da saída será o template do block.

# Instanciação de block

Portanto, dentro de um arquivo XML de Layout, um <block /> tem um "tipo" que é realmente um URI de Nome de Classe Agrupada

    <block type="page/html" ...
    <block type="page/template_links" ...

O URI faz referência a um local na configuração global (diga comigo). 

A primeira parte do URI (na página de exemplos acima ) será usada para consultar a configuração global e encontrar o nome da classe da página. 

A segunda parte do URI (nos dois exemplos acima, html e template_links ) será anexada ao nome da classe base para criar o nome da classe que o Magento deve instanciar.

Iremos percorrer a página / html como exemplo. Primeiro, o Magento procura o nó de configuração global no arquivo

app / code / core / Mage / Page / etc / config.xml

e encontra

    <página>
        <class> Mage_Page_Block </class>
    </page>

Isso nos dá o prefixo da classe base Mage_Page_Block . Em seguida, a segunda parte do URI ( html ) é anexada ao nome da classe para fornecer o nome final da classe Block Mage_Page_Block_Html . Esta é a classe que será instanciada.

Se criarmos um block com o mesmo nome de um block já existente, a nova instância do block substituirá a instância original. Foi o que fizemos no arquivo local.xml acima.

    <layout version="0.1.0">
        <default>
            <block type="page/html" name="root" output="toHtml" template="magentotutorial/helloworld/simple_page.phtml" />
        </default>
    </layout>

O block chamado root foi substituído pelo nosso block, que aponta para um arquivo de template phtml diferente.

# Usando referências

`<reference name = ""/>` conectará todas as declarações XML contidas em um block existente com o nome especificado. 

Os nós `<block />` contidos serão atribuídos como blocos filhos ao block pai referenciado.

    <layout version="0.1.0">
        <default>
            <block type="page/html" name="root" output="toHtml" template="page/2columns-left.phtml">
                <!-- ... sub blocks ... -->
            </block>
        </default>
    </layout>

Em um arquivo de layout diferente:

    <layout version="0.1.0">
        <default>
            <reference name="root">
                <!-- ... another sub block ... -->
                <block type="page/someothertype" name="some.other.block.name" template="path/to/some/other/template" />
            </reference>
        </default>
    </layout>

Mesmo que o block raiz seja declarado em um arquivo XML de layout separado, o novo block é adicionado como um block filho. 

O Magento cria inicialmente um block de página / html chamado root . Então, quando mais tarde encontrar a referência com o mesmo nome ( raiz ), atribuirá o novo block some.other.block.name como filho do block raiz.

Como os arquivos de layout são gerados
Portanto, temos uma compreensão um pouco melhor do que está acontecendo com o XML de layout, mas de onde vem esse arquivo XML?

Para responder a essa pergunta, precisamos introduzir dois novos conceitos; handles e o layout do package.

- handles Cada solicitação de página no Magento irá gerar vários identificadores exclusivos. O módulo Layoutview pode mostrar esses identificadores usando um URL semelhante a http://example.com/helloworld/index/index?showLayout=handles

Você deve ver uma lista semelhante à seguinte (dependendo da sua configuração)

default
STORE_bare_us
THEME_frontend_default_default
helloworld_index_index
customer_logged_out

Cada um deles é um identificador. As handles são colocadas em vários locais dentro do sistema Magento. 

Os dois sobre os quais queremos prestar atenção são defaulfe helloworld_index_index . O identificador defaulfestá presente em todas as solicitações no sistema Magento. O identificador helloworld_index_index é criado combinando o nome da rota (helloworld), o nome do controlador de action (índice) e o método de action do controlador de action (índice) em uma única sequência.

Isso significa que cada método possível em um controlador de action tem um identificador associado a ele.

Lembre-se de que "index" é o defaulfdo Magento para Controladores de Ação e Métodos de Ação, portanto, a seguinte solicitação

http://example.com/helloworld/?showLayout=handles

Também produzirá um identificador chamado helloworld_index_index

# Layout do package

É um arquivo XML grande que contém todas as configurações de layout possíveis para uma instalação específica do Magento. Vamos dar uma olhada nisso usando o módulo Layoutview

http://example.com/helloworld/index/index?showLayout=package

Você deve ver um arquivo XML muito grande. Este é o layout do package. Esse arquivo XML é criado combinando o conteúdo de todos os arquivos de layout XML do tema (ou package) atual. Para a instalação defaulf, isso é em

aplicativo / design / front-end / base / defaulf/ layout /

Nos bastidores, há as seções <frontend> <layout> <updates /> e <adminhtml> <layout> <updates /> da configuração global que contém nós com todos os nomes de arquivos para carregar na respectiva área.

Depois que os arquivos listados na configuração forem combinados, o Magento se fundirá em um último arquivo xml, local.xml. Este é o arquivo em que você pode adicionar suas personalizações à sua instalação do Magento.

# Combinando handles e o layout do package

se você olhar para o Layout do package, verá algumas tags familiares, como `<block />` e `<reference />`, mas todas elas são cercadas por tags que parecem

    <defaulf/>
    <catalogsearch_advanced_index />
    etc ...

Essas são todas as tags de manipulação. O Layout de uma solicitação individual é gerado, agarrando todas as seções do Layout da Embalagem que correspondem a qualquer Identificador da solicitação.

Portanto, no nosso exemplo acima, nosso layout está sendo gerado, pegando as tags das seções a seguir

    <defaulf/>
    <STORE_bare_us />
    <THEME_frontend_default_default />
    <helloworld_index_index />
    <customer_logged_out />

Há uma tag adicional que você precisa conhecer no Layout do package. A tag `<update />` permite incluir as tags de outro identificador. Por exemplo

    <customer_account_index>
        <! - ... ->
        <identificador de atualização = "customer_account" />
        <! - ... ->
    </customer_account_index>

Está dizendo que as solicitações com um identificador customer_account_index devem incluir `<blocks />` s do identificador `<customer_account />.`

    <layout version="0.1.0">
        <default>
            <block type="page/html" name="root" output="toHtml" template="magentotutorial/helloworld/simple_page.phtml" />
        </default>
    </layout>

para local.xml significa que substituímos a tag "root". com um block diferente. 

Ao colocar isso no identificador `<default />`, garantimos que essa substituição ocorra para cada solicitação de página no sistema. Provavelmente não é isso que queremos.

Se você for a qualquer outra página do seu site Magento, perceberá que elas são brancas em branco ou têm o mesmo fundo vermelho que a sua página do hello world. 

Vamos alterar o arquivo local.xml para que ele se aplique apenas à página hello world. Faremos isso alterando o defaulf para usar o identificador de nome de action completo ( helloworld_index_index ).

    <layout version="0.1.0">
        <helloworld_index_index>
            <block type="page/html" name="root" output="toHtml" template="magentotutorial/helloworld/simple_page.phtml" />
        </helloworld_index_index>
    </layout>

Limpe o cache do Magento e o restante das páginas deve ser restaurado.

No momento, isso se aplica apenas ao nosso método de action de índice. Vamos adicioná-lo também ao método de action de despedida. No seu Action Controller, modifique a action de adeus para que pareça

    public function goodbyeAction() {
        $this->loadLayout();
        $this->renderLayout();
    }

Se você carregar o URL a seguir, notará que ainda está recebendo o layout Magento defaulf.

http://example.com/helloworld/index/goodbye

Precisamos adicionar um identificador para o nome completo da action ( helloworld_index_goodbye ) ao nosso arquivo local.xml. Em vez de especificar um novo `<block />`, vamos usar a tag update para incluir o identificador helloworld_index_index .

    <layout version="0.1.0">
        <!-- ... -->
        <helloworld_index_goodbye>
            <update handle="helloworld_index_index" />
        </helloworld_index_goodbye>
    </layout>

Carregar as páginas a seguir (depois de limpar o cache do Magento) agora deve produzir resultados idênticos.

http://example.com/helloworld/index/index
http://example.com/helloworld/index/goodbye

# Iniciando a saída e getChildHtml

Em uma configuração defaulf, a saída começa no block chamado root (porque possui um atributo de saída). Substituímos o template do root com o nosso

template = "magentotutorial / helloworld / simple_page.phtml"
Os templates são referenciados na pasta raiz do tema atual. Nesse caso, isso é

app / design / frontend / base / defaulf

portanto, precisamos detalhar nossa página personalizada. A maioria dos templates Magento são armazenados em

app / design / frontend / base / defaulf/ templates

Combinar isso nos dá o caminho completo

app / design / frontend / base / defaulf/ templates / magentotutorial / helloworld / simple_page.phtml

Altere seu identificador `<helloworld_index_index />` em local.xml para que se pareça com o seguinte

    <helloworld_index_index>
        <block type="page/html" name="root" output="toHtml" template="magentotutorial/helloworld/simple_page.phtml">
            <block type="customer/form_register" name="customer_form_register" template="customer/form/register.phtml"/>
        </block>
    </helloworld_index_index>

Estamos adicionando um novo block aninhado em nossa raiz. Este é um block que é distribuído com Magento e exibirá um formulário de registro de cliente. 

Ao nested esse block em nosso block raiz, disponibilizamos para ser puxado para nosso template simple_page.html . 

Em seguida, usaremos o método getChildHtml do block em nosso arquivo simple_page.phtml . Edite simple_page.html para que fique assim

    <body>
        <? php echo $ this-> getChildHtml ('customer_form_register'); ?>
    </body>

Limpe o cache do Magento e recarregue a página. Você verá o formulário de registro do cliente em seu fundo vermelho. 

Magento também tem um block chamado top.links. Vamos tentar incluir isso. Altere seu arquivo simple_page.html para que ele leia

    <body>
        <h1> Links </h1>
        <? php echo $ this-> getChildHtml ('top.links'); ?>
    </body>

Ao recarregar a página, você notará que o título dos `<h1>` Links `</h1>` está sendo processado, mas nada está sendo processado para top.links. 

Isso porque não o adicionamos ao local.xml. O método getChildHtml pode incluir apenas blocos especificados como sub-blocos no layout. 

Isso permite ao Magento instanciar apenas os blocos necessários e também permite definir templates de diferença para os blocos com base no contexto.

Vamos adicionar o block top.links ao nosso local.xml

    <helloworld_index_index>
        <block type="page/html" name="root" output="toHtml" template="magentotutorial/helloworld/simple_page.phtml">
            <block type="page/template_links" name="top.links"/>
            <block type="customer/form_register" name="customer_form_register" template="customer/form/register.phtml"/>
        </block>
    </helloworld_index_index>

Limpe seu cache e recarregue a página. Agora você deve ver o módulo top.links.

O uso da tag `<action />` nos permite chamar métodos PHP públicos das classes de block. Portanto, em vez de alterar o template do block raiz substituindo a instância do block pela nossa, podemos usar uma chamada para setTemplate .

    <layout version="0.1.0">
        <helloworld_index_index>
            <reference name="root">
                <action method="setTemplate">
                    <template>magentotutorial/helloworld/simple_page.phtml</template>
                </action>
                <block type="page/template_links" name="top.links"/>
                <block type="customer/form_register" name="customer_form_register" template="customer/form/register.phtml"/>
            </reference>
        </helloworld_index_index>
    </layout>

Esse XML de layout primeiro definirá a propriedade do template do block raiz e, em seguida, adicionará os dois blocos que usamos como blocos filhos. Depois que limparmos o cache, o resultado deverá ter a mesma aparência de antes.

O benefício de usar a `<action />` é a mesma instância de block que foi criada anteriormente e todas as outras associações pai / filho ainda existem. Por esse motivo, essa é uma maneira mais prova de atualização de implementar nossas alterações.

Todos os argumentos para o método da action precisam ser agrupados em um nó filho individual da tag <action />. O nome desse nó não importa, apenas a ordem dos nós. Poderíamos ter escrito o nó de action do exemplo anterior da seguinte maneira com o mesmo efeito.

    <action method = "setTemplate">
        <some_new_template> magentotutorial / helloworld / simple_page.phtml </some_new_template>
    </action>

Isso é apenas para ilustrar que os nomes dos nós dos argumentos da action são arbitrários.
