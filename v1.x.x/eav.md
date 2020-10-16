## Magento Estrutura de banco de dados Magento EAV

O que é EAV?
EAV significa entity, attribute e value. 

- A entity representa itens de dados do Magento, como produtos, categorias, clientes e pedidos. Cada entity (produto, categoria etc) terá seu próprio registro de entity no banco de dados.

- Os attributes representam itens de dados que pertencem a uma entity. Por exemplo, a entity do produto possui attributes como nome, preço, status e muito mais.

- O value é o mais simples de entender, pois é simplesmente um value vinculado a um attribute.

Para entender melhor isso, vamos considerar a entity do produto. Cada entity do produto terá uma série de attributes, sendo um o nome do attribute. 

Cada produto terá um value para o nome do attribute (e todos os outros attributes). Isso pode não estar claro ainda, mas continue lendo!

---
## Como o EAV funciona?

Antes do Magento, os bancos de dados pareciam muito mais simples. Se você estava projetando um aplicativo de comércio eletrônico, tinha uma tabela que continha todas as informações do seu produto, outra que continha as informações da categoria e talvez outra tabela que unisse essas duas. 

Isso é simples e fácil de entender, onde o Magento possui quase 40 tabelas para os produtos e as categorias! Para entender o porquê, vamos usar a tabela de produtos como exemplo.

Em vez de armazenar todas as informações do produto em uma tabela, o Magento divide essas informações em sub-tabelas. A tabela superior nesta hierarquia é catalog_product_entity . 

Se você der uma olhada nesta tabela no phpMyAdmin, verá que ela inclui informações básicas simples para um produto e não parece incluir nenhuma informação útil além da SKU! Felizmente, usando esta tabela, é possível criar um registro completo do produto a partir das tabelas de attributes e values.

Para começar a criar um registro completo do produto, você precisará começar a associar attributes à tabela de entitys do produto. 

Antes de fazer isso, dê uma olhada na tabela chamada eav_attribute . eav_attribute é o principal repositório de attributes do Magento e é usado para armazenar attributes de todas as entitys diferentes (produto, cliente, pedido, categoria etc.). 

Abra esta tabela no phpMyAdmin e clique em procurar. Observe que existem centenas de attributes diferentes, alguns até com o mesmo nome? No começo, isso me confundiu porque não tinha certeza de como o Magento poderia diferenciar os dois attributes diferentes chamados name. 

Como o Magento sabia qual era o produto e qual era para uma categoria? Como é geralmente o caso do Magento, um pouco de pesquisa me levou a uma resposta extremamente 

simples:entity_type_id ! Cada entity (produto, categoria, cliente etc) recebe um entity_type_id . 

Para descobrir isso, volte para catalog_product_entity e procure o campo entity_type_id . O value para cada registro nessa tabela deve ser 4, pois foi designado como entity_type_id para produtos. 

Se você estava a olhar em catalog_category_entity você deve ver um diferente entity_type_id . Usando esse value e o código do attribute, é possível carregar os attributes para um produto ou qualquer entity.

Considere as seguintes consultas

    # Load all product attributes
      SELECT attribute_code FROM eav_attribute 
      WHERE entity_type_id = 4;

    # Load a single product attributes
      SELECT attribute_code FROM eav_attribute 
      WHERE entity_type_id = 4 AND attribute_code = 'name';

Agora que você pode obter attributes e entitys, é hora de começar a obter values. 

A maneira como os values são divididos depende do seu tipo. Por exemplo, todos os preços e outros attributes decimais são armazenados em catalog_product_entity_decimal, onde todas as cadeias de texto curtas são armazenadas em catalog_product_varchar .

Para descobrir que cada attribute tabela é armazenada em, Magento usa a coluna backend_type na tabela eav_attribute. 

Se você executar a consulta a seguir, poderá descobrir o tipo de back-end para o attribute do produto 'name'.

    SELECT attribute_code, backend_type FROM eav_attribute 
      WHERE entity_type_id = 4 AND attribute_code = 'name';

Esperamos que a consulta acima retorne o backchar_type varchar , que é o tipo correto para o nome e todas as outras seqüências de texto curtas. 

Com base no que foi dito acima, podemos determinar que o value para o attribute name será armazenado em catalog_product_entity_varchar . O que você acha que será produzido pela seguinte consulta? Pense e copie-o no phpMyAdmin e veja se você está certo.

    SELECT e.entity_id AS product_id, var.value AS product_name
      FROM catalog_product_entity e, eav_attribute eav, catalog_product_entity_varchar var
      WHERE 
          e.entity_type_id = eav.entity_type_id 
          AND eav.attribute_code = 'name' 
          AND eav.attribute_id = var.attribute_id
          AND var.entity_id = e.entity_id

O código acima lista o nome e o ID de cada produto em seu banco de dados.

Se você estiver executando um Magento de várias lojas, veja como adaptar o código acima para incluir apenas produtos de uma determinada loja.

    SELECT e.entity_id AS product_id, var.value AS product_name
      FROM catalog_product_entity e, eav_attribute eav, catalog_product_entity_varchar var
      WHERE 
          e.entity_type_id = eav.entity_type_id 
          AND eav.attribute_code = 'name' 
          AND eav.attribute_id = var.attribute_id
          AND var.entity_id = e.entity_id
          AND var.store_id = 0

---
## Por que o EAV é usado?

O EAV é usado porque é muito mais escalável que a estrutura normalizada do banco de dados. Os desenvolvedores podem adicionar attributes a qualquer entity (produto, categoria, cliente, pedido etc.) sem modificar a estrutura do banco de dados principal.

Quando um attribute personalizado é adicionado, nenhuma lógica deve ser adicionada para forçar o Magento a salvar esse attribute, pois ele já está todo incorporado no modelo; desde que os dados estejam definidos e o attribute tenha sido criado, o modelo será salvo!

Quais são as desvantagens do EAV?
Uma grande desvantagem do EAV é a velocidade. Com os dados da entity sendo tão fragmentados, a criação de um registro inteiro da entity exige muitas junções caras da tabela. Felizmente, a equipe da Varien implementou um excelente sistema de cache, permitindo que os desenvolvedores armazenem em cache informações que geralmente não são alteradas.

Outro problema do EAV é a curva de aprendizado, o que significa que muitos desenvolvedores juniores desistem antes que possam realmente ver a simplicidade dele. Embora não haja uma solução rápida para isso, espero que este artigo ajude as pessoas a começar a superar esse problema.

- Conclusão
entity, attribute, value é uma excelente estrutura de banco de dados e tem sido uma parte essencial para o sucesso do Magento e, portanto, é importante que os desenvolvedores entendam como ele funciona. Existem também muitas aplicações para esse conhecimento e estou confiante se você trabalhar no Magento por tempo suficiente para encontrar algumas!