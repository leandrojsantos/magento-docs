# Varien Data Collections

Uma collection de coisas
Primeiro, vamos criar alguns novos objetos.

    $ coisa_1 = novo Varien_Object ();
    $ thing_1-> setName ('Richard');
    $ coisa_1-> setAge (24);

    $ coisa_2 = novo Varien_Object ();
    $ thing_2-> setName ('Jane');
    $ coisa_2-> setAge (12);

    $ coisa_3 = novo Varien_Object ();
    $ thing_3-> setName ('Spot');
    $ coisa_3-> setLastName ('O Cão');
    $ coisa_3-> setAge (7);

A classe Varien_Object define o objeto do qual todos os model Magento herdam. 

Esse é um padrão comum em sistemas orientados a objetos e garante que você sempre tenha uma maneira de adicionar métodos / funcionalidades facilmente a todos os objetos em seu sistema sem precisar editar todos os arquivos de classe.

Qualquer objeto que se estenda de Varien_Object possui getter e setters mágicos que podem ser usados ​​para definir propriedades de dados. Faça uma tentativa

var_dump ($ coisa_1-> getName ());
Se você não souber qual é o nome da propriedade que deseja, poderá extrair todos os dados como uma array

var_dump ($ coisa_3-> getData ());
O exemplo acima fornecerá uma array semelhante a

    array
    'name' => string 'Spot' (length=4)
    'last_name' => string 'The Dog' (length=7)
    'age' => int 7


$ coisa_1-> setLastName ('Smith');

A capacidade de fazer esse tipo de coisa faz parte do poder do PHP5, e o estilo de desenvolvimento que uma certa classe de pessoas entende quando dizem "Programação Orientada a Objetos".

Então, agora que temos alguns objetos, vamos adicioná-los a uma collection. Lembre-se, uma collection é como uma array, mas é definida por um programador PHP.

    $ collection_of_things = new Varien_Data_Collection ();
    $ collection_of_things
        -> addItem ($ coisa_1)
        -> addItem ($ coisa_2)
        -> addItem ($ coisa_3);

O Varien_Data_Collection é a collection da qual a maioria das collections de dados Magento herda. Qualquer método que você possa chamar em um Varien_Data_Collection, você pode chamar collections mais altas na cadeia (veremos mais sobre isso mais adiante)

O que podemos fazer com uma collection? Por um lado, com pode usar foreach para iterar sobre ele

    foreach($collection_of_things as $thing)
    {
        var_dump($thing->getData());
    }

Também existem atalhos para retirar o primeiro e o último itens

    var_dump ($ collection_of_things-> getFirstItem () -> getData ());
    var_dump ($ collection_of_things-> getLastItem () -> getData ());

Deseja seus dados da collection como XML? Existe um método para isso

var_dump ($ collection_of_things-> toXml ());

Quer apenas um campo específico?

var_dump ($ collection_of_things-> getColumnValues ​​('nome'));

A equipe do Magento nos deu alguns recursos de filtragem rudimentares.

var_dump ($ collection_of_things-> getItemsByColumnValue ('nome', 'Spot'));
Coisa legal.

# collections de model

Isso significa que, se você tem, por exemplo, uma collection de produtos, pode fazer o mesmo tipo de coisa. Vamos dar uma olhada

public function testAction()
{
    $collection_of_products = Mage::getModel('catalog/product')->getCollection();
    var_dump($collection_of_products->getFirstItem()->getData());
}

A maioria dos objetos do Magento Model possui um método chamado getCollection que retornará uma collection que, por padrão, é inicializada para retornar todos os objetos desse tipo no sistema.

Uma observação rápida: as collections de dados do Magento contêm muitas lógicas complicadas que lidam com quando usar um índice ou cache, bem como a lógica do sistema de entidades EAV. 

Chamadas sucessivas de método para a mesma collection ao longo de sua vida útil geralmente podem resultar em comportamento inesperado. 

Por esse motivo, todos os exemplos a seguir são agrupados em uma única ação de método. Eu recomendo fazer o mesmo enquanto estiver experimentando. 

A collection de produtos, assim como muitas outras collections do Magento, também têm a classe Varien_Data_Collection_Db em sua cadeia ancestral.

Isso nos dá muitos métodos úteis. Por exemplo, se você deseja ver a instrução select que sua collection está usando

    public function testAction()
    {
        $collection_of_products = Mage::getModel('catalog/product')->getCollection();
        var_dump($collection_of_products->getSelect()); //might cause a segmentation fault
    }

A saída do acima será

    object(Varien_Db_Select)[94]
      protected '_bind' =>
        array
          empty
      protected '_adapter' =>
    ...

Vamos ver isso como uma string mais útil.

    public function testAction()
    {
        $collection_of_products = Mage::getModel('catalog/product')->getCollection();
        //var_dump($collection_of_products->getSelect()); //might cause a segmentation fault
        var_dump(
            (string) $collection_of_products->getSelect()
        );
    }

Às vezes, isso resultará em uma seleção simples

    'SELECT `e`.* FROM `catalog_product_entity` AS `e`'

Outras vezes, algo um pouco mais complexo

    string 'SELECT `e`.*, `price_index`.`price`, `price_index`.`final_price`, IF(`price_index`.`tier_price`, 
    LEAST(`price_index`.`min_price`, `price_index`.`tier_price`), `price_index`.`min_price`) AS `minimal_price`, 
    `price_index`.`min_price`, `price_index`.`max_price`, `price_index`.`tier_price` FROM `catalog_product_entity` 
    AS `e` INNER JOIN `catalog_product_index_price` AS `price_index` ON price_index.entity_id = e.entity_id AND 
    price_index.website_id = '1' AND price_index.customer_group_id = 0'

Você pode adicioná-los todos usando o método addAttributeToSelect

    $collection_of_products = Mage::getModel('catalog/product')
        ->getCollection()
        ->addAttributeToSelect('*');  //the asterisk is like a SQL SELECT * FROM ...

Ou você pode adicionar apenas um

// ou apenas um

    $collection_of_products = Mage::getModel('catalog/product')
        ->getCollection()
        ->addAttributeToSelect('meta_title');

ou encadear vários

// ou apenas um

    $collection_of_products = Mage::getModel('catalog/product')
        ->getCollection()
        ->addAttributeToSelect('meta_title')
        ->addAttributeToSelect('price');

# Filtrando collections de bancos de dados

O método mais importante em uma collection de banco de dados é addFieldToFilter . 

Isso adiciona suas cláusulas WHERE à consulta SQL usada nos bastidores. 

Considere esse pedaço de código, execute no banco de dados de amostra (substitua seu próprio SKU se você estiver usando um conjunto diferente de dados do produto)

    public function testAction()
    {
        $collection_of_products = Mage::getModel('catalog/product')
            ->getCollection();
        $collection_of_products->addFieldToFilter('sku','n2610');

        . count($collection_of_products) . ' item(s)';
        var_dump($collection_of_products->getFirstItem()->getData());
    }

O primeiro parâmetro de addFieldToFilter é o atributo pelo qual você deseja filtrar. 

O segundo é o valor que você está procurando. Aqui estamos adicionando um filtro de sku para o valor n2610 .

O segundo parâmetro também pode ser usado para especificar o tipo de filtragem que você deseja fazer. É aqui que as coisas ficam um pouco complicadas e vale a pena aprofundar um pouco mais.

Então, por padrão, o seguinte

$ collection_of_products-> addFieldToFilter ('sku', 'n2610');

é (essencialmente) equivalente a

where sku = "n2610"

Dê uma olhada por si mesmo. Executando o seguinte

public function testAction()
{
    var_dump(
    (string)
    Mage::getModel('catalog/product')
    ->getCollection()
    ->addFieldToFilter('sku','n2610')
    ->getSelect());
}

vai render

SELECT `e`. * FROM` catalog_product_entity` AS `e` WHERE (e.sku = 'n2610') '

Lembre-se de que isso pode ficar complicado rapidamente se você estiver usando um atributo EAV. Adicionar um atributo

var_dump(
    (string)
    Mage::getModel('catalog/product')
        ->getCollection()
        ->addAttributeToSelect('*')
        ->addFieldToFilter('meta_title','my title')
        ->getSelect()
);

e a consulta fica complicada.

SELECT `e`.*, IF(_table_meta_title.value_id>0, _table_meta_title.value, _table_meta_title_default.value) AS `meta_title`
FROM `catalog_product_entity` AS `e`
INNER JOIN `catalog_product_entity_varchar` AS `_table_meta_title_default`
    ON (_table_meta_title_default.entity_id = e.entity_id) AND (_table_meta_title_default.attribute_id='103')
    AND _table_meta_title_default.store_id=0
LEFT JOIN `catalog_product_entity_varchar` AS `_table_meta_title`
    ON (_table_meta_title.entity_id = e.entity_id) AND (_table_meta_title.attribute_id='103')
    AND (_table_meta_title.store_id='1')
WHERE (IF(_table_meta_title.value_id>0, _table_meta_title.value, _table_meta_title_default.value) = 'my title')


# Outros operadores de comparação

"e se eu quiser algo diferente de igual por consulta"? Diferente, maior que, menor que etc. 

O segundo parâmetro do método addFieldToFilter também o abordou. Ele suporta uma sintaxe alternativa, onde, em vez de passar uma string, você passa um único elemento Array.

A key dessa array é o tipo de comparação que você deseja fazer.

O valor associado a essa key é o valor pelo qual você deseja filtrar. Vamos refazer o filtro acima, mas com esta sintaxe explícita

    public function testAction()
    {
        var_dump(
            (string)
            Mage::getModel('catalog/product')
                ->getCollection()
                ->addFieldToFilter('sku', array('eq'=>'n2610'))
                ->getSelect()
        );
    }

Chamando nosso filtro

addFieldToFilter ('sku', array ('eq' => 'n2610'))

Como você pode ver, o segundo parâmetro é uma array PHP. Sua key é eq , que significa iguais . O valor dessa key é n2610 , que é o valor que estamos filtrando.

Listados abaixo estão todos os filtros, juntamente com um exemplo de seus equivalentes SQL.

    array("eq"=>'n2610')
    WHERE (e.sku = 'n2610')

    array("neq"=>'n2610')
    WHERE (e.sku != 'n2610')

    array("like"=>'n2610')
    WHERE (e.sku like 'n2610')

    array("nlike"=>'n2610')
    WHERE (e.sku not like 'n2610')

    array("is"=>'n2610')
    WHERE (e.sku is 'n2610')

    array("in"=>array('n2610'))
    WHERE (e.sku in ('n2610'))

    array("nin"=>array('n2610'))
    WHERE (e.sku not in ('n2610'))

    array("notnull"=>true)
    WHERE (e.sku is NOT NULL)

    array("null"=>true)
    WHERE (e.sku is NULL)

    array("gt"=>'n2610')
    WHERE (e.sku > 'n2610')

    array("lt"=>'n2610')
    WHERE (e.sku < 'n2610')

    array("gteq"=>'n2610')
    WHERE (e.sku >= 'n2610')

    array("moreq"=>'n2610') //a weird, second way to do greater than equal (Doesn't work on > 1.8 CE EDITION do the same as eq)
    WHERE (e.sku >= 'n2610')

    array("lteq"=>'n2610')
    WHERE (e.sku <= 'n2610')

    array("finset"=>array('n2610'))
    WHERE (find_in_set('n2610',e.sku))

    array('from'=>'10','to'=>'20')
    WHERE e.sku >= '10' and e.sku <= '20'


Os condicionais in , nin e finset permitem passar uma array de valores. 

Ou seja, é permitido que a parte do valor da sua array de filtros seja uma array.

    array ("in" => array ('n2610', 'ABC123')
    WHERE (e.sku em ('n2610', 'ABC123'))

A palavra-key NULL é especial na maioria dos tipos de SQL. Normalmente, não funciona bem com o operador de igualdade padrão ( = ). 

A especificação de notnull ou null como seu tipo de filtro fornecerá a sintaxe correta para uma comparação NULL, ignorando o valor que você passar

array ("notnull" => true)
where (e.sku NÃO É NULL)


# AND ou OR, ou são OR e AND?

Finalmente, chegamos aos operadores booleanos. 

É o raro momento em que estamos filtrando apenas um atributo. Você pode encadear várias chamadas para addFieldToFilter para obter um número de consultas "AND".

    public function testAction()
    {
        $filter_a = array('like'=>'a%');
        $filter_b = array('like'=>'b%');
        echo
            (string)
            Mage::getModel('catalog/product')
                ->getCollection()
                ->addFieldToFilter('sku', array($filter_a, $filter_b))
                ->getSelect();
    }

Ao encadear várias chamadas como acima, produziremos uma cláusula where que se parece com a seguinte

WHERE (e.sku como 'a%') AND (e.sku como 'b%')

Se você deseja criar uma consulta OR , precisa passar uma array de filtro arrayes como o segundo parâmetro. Acho melhor atribuir seu filtro individual Arrays a variáveis

função pública testAction ()
{
    $ filter_a = array ('like' => 'a%');
    $ filter_b = array ('like' => 'b%');
}

e depois atribuir uma array de todas as minhas variáveis ​​de filtro

    public function testAction()
    {
        $filter_a = array('like'=>'a%');
        $filter_b = array('like'=>'b%');
        echo
            (string)
            Mage::getModel('catalog/product')
                ->getCollection()
                ->addFieldToFilter('sku', array($filter_a, $filter_b))
                ->getSelect();
    }

No interesse de ser explícito, aqui está a array acima mencionada de arrayes de filtro.

array ($ filter_a, $ filter_b)

Isso nos dará uma cláusula WHERE que se parece com a seguinte

WHERE (((e.sku como 'a%') ou (e.sku como 'b%'))) 
