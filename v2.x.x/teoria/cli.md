# Exemplos de comando mais utilizados no cli(Interface de Linha de Comando) do magento

* Como estamos usando uma img docker para rodar magento2 entre nela com cmd `docker-compose exec php73-fpm bash` feito isso verifique o nome da pasta que esta o magento com cmd `ls -la`e entre nela 

Para entra CLI do magento = `php bin/magento`
nele havera a descricao do cmd,

Usar CLI do magento = `php bin/magento <cmd>`

Sample data(loja de exemplo) = `php bin/magento sampledata:deploy` apos ele terminar todos os modulos e necessario habilitar os novos modulos `php bin/magento module:status` para ver quais estao desabilitados e `php bin/magento module:enable <nome_do_modulo>` feito isso `php bin/magento setup:di:compile` para sincronizar e dependency injection configuration 