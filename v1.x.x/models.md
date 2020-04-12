# Modelos MVC PHP tradicionais

A implementação de uma "camada de modelos" é uma grande parte de qualquer estrutura MVC. Ele representa os dados do seu aplicativo e a maioria dos aplicativos é inútil sem dados.

Os modelos Magento têm um papel ainda maior, pois geralmente contêm a "Business Logic" que é frequentemente relegada aos métodos Controller ou Helper em outras estruturas PHP MVC.

Abordagem ORM (Object Relational Mapping). Aqui, um desenvolvedor está lidando estritamente com objetos. 

As propriedades são definidas e, quando um método de salvamento é chamado no objeto, os dados são gravados automaticamente no banco de dados.

# Modelos Magento

A maioria dos modelos Magento pode ser categorizada de uma de duas maneiras. 

* modelo básico, do tipo ActiveRecord um objeto-uma-tabela

* modelo EAV (Entity Attribute Value). 

Cada modelo também recebe uma coleção de modelos. Coleções são objetos PHP usados ​​para armazenar várias instâncias individuais do Magento Model.

A equipe do Magento implementou as interfaces da biblioteca padrão do PHP de IteratorAggregate e Countable para permitir que cada tipo de modelo tenha seu próprio tipo de coleção. 

Os modelos Magento não contêm código para conexão com o banco de dados. Em vez disso, cada Modelo usa uma class modelResource , usada para se comunicar com o servidor de banco de dados (por meio de um objeto de adaptador de leitura e um de gravação). 

Ao desacoplar o Modelo lógico e o código que fala com o banco de dados, é teoricamente possível escrever novas classs de recursos para diferentes esquemas e plataformas de banco de dados, mantendo os próprios Modelos intocados.

# Ativar modo de desenvolvedor

Algo que você deve fazer no desenvolvimento 
- mas nunca na produção 
- é ativar o modo de desenvolvedor do Magento que, entre outras coisas, exibe exceções no seu navegador. É útil para depurar seu código.

Ative o modo de desenvolvedor de qualquer uma das seguintes maneiras:

    Edite o .htaccessarquivo no diretório raiz do Magento para adicionarSetEnv MAGE_IS_DEVELOPER_MODE "true"

* Criando um modelo básico
Para começar, vamos criar um modelo básico do Magento. A tradição PHP MVC insiste em que modelemos uma postagem no blog. Os passos que precisaremos seguir são

* Crie um novo módulo "Weblog"
* Crie uma tabela de banco de dados para nosso modelo
* Adicione informações do modelo à configuração de um modelo chamado Blogpost
* Adicione informações do recurso de modelo à configuração do modelo de post do blog
* Adicione um adaptador de leitura à configuração do modelo do Blogpost
* Adicione um adaptador de gravação à configuração para o modelo do Blogpost
* Adicionar um arquivo de class PHP para o modelo do Blogpost
Inclua um arquivo de class PHP para o Modelo de Recursos do Blogpost
* Instanciar o modelo

* Criar um módulo de blog Controlador de ação de índice com uma ação chamada "testModel". Como sempre, os exemplos a seguir assumem o nome do pacote "Magentotutorial".

Em `Magentotutorial / Weblog / etc / config.xml`, configure a seguinte rota

    <frontend>
        <routers>
            <weblog>
                <use>standard</use>
                <args>
                    <module>Magentotutorial_Weblog</module>
                    <frontName>weblog</frontName>
                </args>
            </weblog>
        </routers>
    </frontend>
  
E adicione o seguinte Action Controller em

    class Magentotutorial_Weblog_IndexController extends Mage_Core_Controller_Front_Action {
        public function testModelAction() {
            echo 'Setup!';
        }
    }

em Magentotutorial / Weblog / controllers / IndexController.php . Limpe o cache do Magento e carregue o seguinte URL para garantir que tudo foi configurado corretamente.

  http://example.com/weblog/index/testModel

Você deverá ver a palavra "Instalação" em um fundo branco.

Criando a tabela de banco de dados
O Magento possui um sistema para criar e alterar automaticamente seus esquemas de banco de dados, mas, por enquanto, apenas criaremos manualmente uma tabela para o nosso modelo.

Usando a linha de comando ou seu aplicativo GUI MySQL favorito, crie uma tabela com o seguinte esquema

    CREATE TABLE `blog_posts` (
      `blogpost_id` int(11) NOT NULL auto_increment,
      `title` text,
      `post` text,
      `date` datetime default NULL,
      `timestamp` timestamp NOT NULL default CURRENT_TIMESTAMP,
      PRIMARY KEY  (`blogpost_id`)
    )

E, em seguida, preencha-o com alguns dados

    INSERT INTO `blog_posts` VALUES (1,'My New Title','This is a blog post','2010-07-01 00:00:00','2010-07-02 23:12:30');

A configuração global e a criação do modelo
Há três coisas individuais que precisamos configurar para um modelo em nossa configuração.

* Ativando modelos em nosso módulo
* Ativando recursos de modelo em nosso módulo
* Adicione uma configuração de tabela "entidade" ao nosso Recurso de modelo.

Ao instanciar um modelo no Magento, você faz uma ligação como esta

    $model = Mage :: getModel ('weblog / blogpost');

A primeira parte do URI que você passa para obter o Modelo é o Nome do Grupo de Modelos .

Como bom seguir as convenções, esse deve ser o nome (minúsculo) do seu módulo ou, para ser protegido contra conflitos, use o nome do pacote e o nome do módulo (também em minúscula). 

A segunda parte do URI é a versão em minúscula do nome do seu modelo.

Então, vamos adicionar o seguinte XML ao `config.xml` do nosso módulo.

    <global>
        <!-- ... -->
        <models>
            <weblog>
                <class>Magentotutorial_Weblog_Model</class>
                <!--
                need to create our own resource, can't just
                use core_resource
                -->
                <resourceModel>weblog_resource</resourceModel>
            </weblog>
        </models>
        <!-- ... -->
    </global>

A tag externa <weblog/> é o nome do seu grupo, que deve corresponder ao nome do seu módulo. <class/> é o nome BASE que todos os modelos do grupo de blogs terão, também chamado de prefixo da class .

A tag <resourceModel/> indica qual modelo de recurso que os modelos de grupo de blogs devem usar. 

limparmos o cache do Magento e tentarmos instanciar um modelo de post no blog. No seu método `testModelAction`, use o seguinte código

    public function testModelAction() {
        $blogpost = Mage::getModel('weblog/blogpost');
        echo get_class($blogpost);
    }

e recarregue sua página. Você verá um erro como o seguinte:

    include(Magentotutorial/Weblog/Model/Blogpost.php) [function.include]: failed to open stream: No such file or directory

esse arquivo ou diretório não existe
Ao tentar recuperar um modelo de blog / blog , você disse ao Magento para instanciar uma class com o nome

    Magentotutorial_Weblog_Model_Blogpost

O Magento está tentando __autoload incluir este modelo, mas não consegue encontrar o arquivo. 

Vamos criá-lo! Crie a seguinte class no seguinte local

    File: app/code/local/Magentotutorial/Weblog/Model/Blogpost.php
    class Magentotutorial_Weblog_Model_Blogpost extends Mage_Core_Model_Abstract
    {
        protected function _construct()
        {
            $this->_init('weblog/blogpost');
        }
    }

Recarregue sua página e a exceção deve ser substituída pelo nome da sua class.

Todos os modelos básicos que interagem com o banco de dados devem estender a class Mage_Core_Model_Abstract . 

Esta class abstrata obriga a implementar um único método chamado _construct 

( NOTA: este não é o construtor do PHP __construct ). 

Esse método deve chamar o método _init da class com o mesmo URI de identificação que você usará na chamada do método Mage :: getModel .

* A configuração e os recursos globais

Então, configuramos nosso modelo. Em seguida, precisamos configurar nosso recurso de modelo. Recursos de modelo contêm o código que realmente fala com nosso banco de dados. Na última seção, incluímos o seguinte em nossa configuração.

    <resourceModel>weblog_resource</resourceModel>

será usado para instanciar uma class Model Resource. Embora você nunca precise chamá-lo, quando qualquer modelo no grupo de blogs precisar conversar com o banco de dados, o Magento fará a seguinte chamada de método para obter o recurso de modelo

    Mage :: getResourceModel ('weblog / blogpost');

Novamente, weblog é o nome do grupo e blogpost é o modelo. 

O método Mage :: getResourceModel usará o URI weblog / blogpost para inspecionar a configuração global e extrair o valor em <resourceModel> (neste caso, weblog_resource ). 

Em seguida, uma class de modelo será instanciada com o seguinte URI

    weblog_resource / blogpost

configurar nosso recurso,  em nossa seção <models>, adicione

    <global>
        <! - ... ->
        <modelos>
            <! - ... ->
            <weblog_resource>
                <class> Magentotutorial_Weblog_Model_Resource </class>
            </weblog_resource>
        </models>
    </global>

Você está adicionando a tag <weblog_resource/>, que é o valor da tag <resourceModel/> que você acabou de configurar. 

O valor de class é o nome base que todos os seus modelos de recursos terão e deve ser nomeado com o seguinte formato

    Packagename_Modulename_Model_Resource

Portanto, como temos um recurso configurado, vamos tentar carregar alguns dados do modelo. Altere sua ação para se parecer com o seguinte

    public function testModelAction() {
        $params = $this->getRequest()->getParams();
        $blogpost = Mage::getModel('weblog/blogpost');
        echo("Loading the blogpost with an ID of ".$params['id']);
        $blogpost->load($params['id']);
        $data = $blogpost->getData();
        var_dump($data);
    }

E, em seguida, carregue o seguinte URL no seu navegador (depois de limpar o cache do Magento)

http://example.com/weblog/index/testModel/id/1

Você deve ver uma exceção, algo como o seguinte

    Warning: include(Magentotutorial/Weblog/Model/Resource/Blogpost.php) [function.include]: failed to open stream: No such file ....

Como você provavelmente intuiu, precisamos adicionar uma class de recurso para o nosso Model. Todo modelo tem sua própria class de recurso. Adicione a seguinte class no seguinte local

    File: app/code/local/Magentotutorial/Weblog/Model/Resource/Blogpost.php

    class Magentotutorial_Weblog_Model_Resource_Blogpost extends Mage_Core_Model_Resource_Db_Abstract{
            protected function _construct()
            {
                $this->_init('weblog/blogpost', 'blogpost_id');
            }
        }

Novamente, o primeiro parâmetro do método init é a URL usada para identificar o modelo .

O segundo parâmetro é o campo do banco de dados que identifica exclusivamente qualquer coluna específica. Na maioria dos casos, essa deve ser a chave primária. Limpe seu cache, recarregue e você verá

Não é possível recuperar a configuração da entidade: weblog / blogpost
Outra exceção! Quando usamos o blog / blogpost do Model URI , dizemos ao Magento que queremos o blog do Model Group e a entidade do blogpost . 

No contexto de modelos simples que estendem Mage_Core_Model_Resource_Db_Abstract , uma entidade corresponde a uma tabela. Nesse caso, a tabela denominada blog_post que criamos acima. Vamos adicionar essa entidade à nossa configuração XML.

        <models>
            <!-- ... --->
            <weblog_resource>
                <class>Magentotutorial_Weblog_Model_Resource</class>
                <entities>
                    <blogpost>
                        <table>blog_posts</table>
                    </blogpost>
                </entities>
            </weblog_resource>
        </models>

Adicionamos uma nova seção <entidades/> à seção Modelo de recurso de nossa configuração. 

Isso, por sua vez, possui uma seção com o nome de nossa entidade (<blogpost/>) que especifica o nome da tabela de banco de dados que queremos usar para este modelo.

Limpe o cache do Magento, cruze os dedos, recarregue a página

      Loading the blogpost with an ID of 1

      array
        'blogpost_id' => string '1' (length=1)
        'title' => string 'My New Title' (length=12)
        'post' => string 'This is a blog post' (length=19)
        'date' => string '2009-07-01 00:00:00' (length=19)
        'timestamp' => string '2009-07-02 16:12:30' (length=19)

# Operações básicas do modelo

Todos os modelos Magento herdam da class Varien_Object. Esta class faz parte da biblioteca do sistema Magento e não faz parte de nenhum módulo principal do Magento. 

Você pode encontrar este objeto em  lib / Varien / Object.php

Os modelos Magento armazenam seus dados em uma propriedade _data protegida. A class Varien_Object nos fornece vários métodos que podemos usar para extrair esses dados. Você já viu getData , que retornará uma matriz de pares de chave / valor. Este método também pode receber uma chave de cadeia para obter um campo específico.

    $model-> getData ();
    $model-> getData ('título');

Há também um método getOrigData, que retornará os dados do modelo como estavam quando o objeto foi preenchido inicialmente (trabalhando com o método _origData protegido).

    $model-> getOrigData ();
    $model-> getOrigData ('title');

O Varien_Object também implementa alguns métodos especiais através do método magic __call do PHP . 

Você pode obter, definir, desarmar ou verificar a existência de qualquer propriedade usando um método que comece com a palavra obter, definir, desarmar ou tenha e seja seguido pelo nome de uma propriedade camel case.

    $model-> getBlogpostId ();
    $model-> setBlogpostId (25);
    $model-> unsBlogpostId ();
    if ($model-> hasBlogpostId ()) {...}

Por esse motivo, convém nomear todas as colunas do banco de dados com caracteres minúsculos e usar underscores para separar caracteres.

# CRUD, o Caminho Magento

Os modelos Magento suportam a funcionalidade básica Criar, Ler, Atualizar e Excluir do CRUD com métodos de carregamento , salvamento e exclusão .

Você já viu o método de carregamento em ação. Quando transmitido um único parâmetro, o método load retornará um registro cujo campo de ID (definido no recurso do modelo) corresponde ao valor transmitido.

    $blogpost-> load (1);

O método save permitirá que você insira um novo modelo no banco de dados ou atualize um existente. Adicione o seguinte método ao seu Controller

    public function createNewPostAction() {
        $blogpost = Mage::getModel('weblog/blogpost');
        $blogpost->setTitle('Code Post!');
        $blogpost->setPost('This post was created from code!');
        $blogpost->save();
        echo 'post with ID ' . $blogpost->getId() . ' created';
    }

e, em seguida, execute sua ação do controlador carregando o seguinte URL

http://example.com/weblog/index/createNewPost

Agora você deve ver uma postagem salva adicional na sua tabela de banco de dados. 

Em seguida, tente o seguinte para editar sua postagem

    public function editFirstPostAction() {
        $blogpost = Mage::getModel('weblog/blogpost');
        $blogpost->load(1);
        $blogpost->setTitle("The First post!");
        $blogpost->save();
        echo 'post edited';
    }

E, finalmente, você pode excluir sua postagem usando uma sintaxe muito semelhante.

    public function deleteFirstPostAction() {
        $blogpost = Mage::getModel('weblog/blogpost');
        $blogpost->load(1);
        $blogpost->delete();
        echo 'post removed';
    }

# Coleções de modelos

Portanto, ter um único modelo é útil, mas às vezes queremos pegar a lista de modelos. 

Em vez de retornar uma matriz simples de modelos, cada tipo de modelo Magento tem um objeto de coleção exclusivo associado a ele. 

Esses objetos implementam as interfaces PHP IteratorAggregate e Countable, o que significa que elas podem ser passadas para a função count e usadas em cada construção.

    public function showAllBlogPostsAction() {
        $posts = Mage::getModel('weblog/blogpost')->getCollection();
        foreach($posts as $blogpost){
            echo '<h3>'.$blogpost->getTitle().'</h3>';
            echo nl2br($blogpost->getPost());
        }
    }

Carregue o URL da ação,

http://example.com/weblog/index/showAllBlogPosts

e você deve ver uma exceção conhecida (agora).

    Warning: include(Magentotutorial/Weblog/Model/Resource/Blogpost/Collection.php) [function.include]: failed to open stream

Todo modelo tem uma propriedade protegida chamada _resourceCollectionName que contém um URI usado para identificar nossa coleção.

    protected '_resourceCollectionName' => string 'weblog/blogpost_collection'

Por padrão, esse é o mesmo URI usado para identificar nosso Modelo de Recursos, com a cadeia "_collection" anexada ao final. 

O Magento considera Coleções parte do Recurso, portanto esse URI é convertido no nome da class.

Magentotutorial_Weblog_Model_Resource_Blogpost_Collection
Adicione a seguinte class PHP no seguinte local

    File: app/code/local/Magentotutorial/Weblog/Model/Resource/Blogpost/Collection.php
    class Magentotutorial_Weblog_Model_Resource_Blogpost_Collection extends Mage_Core_Model_Resource_Db_Collection_Abstract {
        protected function _construct()
        {
                $this->_init('weblog/blogpost');
        }
    }

Assim como em nossas outras classs, precisamos iniciar nossa coleção com o modelo URI. (blog / blog). 

Execute novamente sua Ação do Controlador e você deverá ver as informações da sua postagem.