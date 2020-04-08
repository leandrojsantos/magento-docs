# Instalação do magento1 usando img docker lemp + xdebug

* Cadastro no site do magento para criar acess keys, no marktingplace

>Download Magento
Etapa 1 - Abra o link https://www.magentocommerce.com/products/downloads/magento/ , baixe ver 1.9.x.x

>Magento Installation
Etapa 2 - Clique no menu suspenso, o arquivo está disponível em .zip, .tar.gz e .tar.bz2 para download.

Etapa 3 - Extraia os arquivos da Web Magento do arquivo no seu computador e faça o upload para o seu servidor ou host local.

Etapa 4 - Magento requer banco de dados MySQL. Portanto, crie um novo banco de dados e usuário / senha vazios (por exemplo, usuário como "root" e senha como "root" ou você pode definir conforme sua conveniência) para o Magento.

Etapa 5 - Abra o navegador e navegue até o caminho do arquivo Magento (por exemplo, http: // localhost / magento) para iniciar sua instalação do Magento. 
Então, você verá uma tela do instalador do Magento

Etapa 6 - Clique no botão Continuar e você obterá a tela Validação para Magento Downloader, conforme mostrado na tela a seguir, insira os detalhes do banco de dados.

Etapa 7 - Em seguida, você verá a tela de implantação do Magento Connect Manager.

Etapa 8 - A tela do Assistente de Instalação do Magento é exibida. 

Etapa 9 - Em seguida, você receberá a tela Localização para selecionar a localidade, o fuso horário e a moeda

Etapa 10 - A próxima tela que aparece é a tela de configuração.
Preencha as informações do banco de dados, como Tipo de banco de dados , Host , Nome do banco de dados , Nome do usuário e Senha do usuário . 

Se você não deseja validar o URL base, marque a caixa de seleção Ignorar validação do URL base antes da próxima etapa e clique no botão Continuar .

Caso o http: // localhost / magento não funcione, use-o como url base - http://127.0.0.1/magento
Esta etapa levará algum tempo, pois o Magento criará as tabelas de banco de dados.

Etapa 11 - Agora, vá para a tela Criar conta de administrador,
Aqui, insira suas informações pessoais, como Nome, Sobrenome e E-mail, e as Informações de login, como Nome de usuário, Senha e Confirmar senha, para o administrador usar no back-end. 

Não precisa se preocupar com o campo Chave de criptografia , pois o Magento irá gerar uma chave na próxima página. Depois de preencher todas as informações, clique no botão Continuar .

Etapa 12 - Copie a chave de criptografia, que será usada para criptografar senhas. Depois, você pode selecionar Frontend ou Backend do novo site Magento.

Etapa 13 - Após a instalação bem-sucedida do Magento, clique no botão Ir para back-end para fazer login no painel de administração.