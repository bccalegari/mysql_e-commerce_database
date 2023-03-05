# Projeto de Banco de Dados: E-Commerce

Projeto de banco de dados para e-commerce com mysql.

## Índice 

- 1. [Modelo Entidade-Relacionamento Estendido](#modelo-entidade-relacionamento-estendido)
- 2. [Arquivos](#arquivos)
- 3. [Tecnologias Utilizadas](#tecnologias-utilizadas)

## Modelo Entidade-Relacionamento Estendido:

[e-commerce_model.pdf](https://github.com/bccalegari/mysql_e-commerce_database/blob/main/e_commerce_model.pdf)
![e-commerce_model](https://user-images.githubusercontent.com/109561086/222974281-24eda4dd-9c99-4618-b178-80fc0bec3b2b.png)

### Entidades: 

- 1. [Dados](#dados)
  - 1. [cliente](#cliente)
  - 2. [cartao](#cartao)
  - 3. [cliente_tem_cartao](#cliente_tem_cartao)
  - 4. [pagamento](#pagamento)
  - 5. [pedido](#pedido)
  - 6. [entrega](#entrega)
  - 7. [endereco](#endereco)
  - 8. [cliente_tem_endereco](#cliente_tem_endereco)
  - 9. [telefone](#telefone)
- 2. [Informações](#informações)
  - 1. [cartao_bandeira](#cartao_bandeira)
  - 2. [pagamento_tipo](#pagamento_tipo)
  - 3. [cartao_bandeira_tem_pagamento_tipo](#cartao_bandeira_tem_pagamento_tipo)
  - 4. [pedido_status](#pedido_status)
  - 6. [entrega_status](#entrega_status)
  - 8. [estado](#estado)
  - 9. [ddd](#ddd)
- 3. [Empresa](#empresa)
  - 1. [empresa](#empresa-1)
  - 2. [unidade](#unidade)

### Dados:

- ### cliente:

A entidade '_cliente_' guarda o registro de todas as contas dos clientes do e-commerce.

  - Quando um cliente deleta a sua conta, não deletamos o seu registro no banco de dados, iremos anonimizar os dados sensíveis como '_cpf_', '_email_' e '_id_telefone_' e fazer uso do atributo '_desativado_' para refletir a deleção dessa conta.

| Atributos               | Constraint  |    Relacionamento    |                                  Descrição                                  |
| ----------------------- | :---------: | :------------------: | :-------------------------------------------------------------------------: |
| id_cliente              |     PK      |          -           |                               Chave primária                                |
| data_conta_criada       |      -      |          -           |                  Data em que a conta do cliente foi criada                  |
| nome                    |      -      |          -           |                               Nome do cliente                               |
| sobrenome               |      -      |          -           |                            Sobrenome do cliente                             |
| sexo                    |      -      |          -           |                           Sexo do cliente (M, F)                            |
| data_de_nascimento      |      -      |          -           |                        Data de nascimento do cliente                        |
| id_telefone             | FK / UNIQUE | telefone.id_telefone |          Chave estrangeira, faz referência ao telefone do cliente           |
| email                   |   UNIQUE    |          -           |                              E-mail do cliente                              |
| cpf                     |   UNIQUE    |          -           |                               CPF do cliente                                |
| senha                   |      -      |          -           |                      Senha do cliente (Criptografada)                       |
| salt                    |   UNIQUE    |          -           |          Dado aleatório, usado na criptografia da senha do cliente          |
| data_ultima_atualizacao |      -      |          -           | Data mais recente em que o cliente atualizou alguma informação de sua conta |
| desativado              |      -      |          -           |                Indica se a conta do cliente está desativada                 |
| data_conta_desativada   |      -      |          -           |                Data em que a conta do cliente foi desativada                |


- ### cartao:

A entidade '_cartão_' guarda todos os cartões salvos pelos clientes em suas contas no e-commerce.

  - Um cliente pode deixar cartões salvos em sua conta para facilitar o processo de compra, ao criar um cartão em sua conta é criado um registro nessa tabela com os dados de seu cartão criptografados.
  - Quando um cliente deleta um cartão salvo de sua conta, o seu vínculo com o cartão na entidade '_cliente_tem_cartao_' será deletado, como um cartão pode estar ligado a mais de um cliente, ele só será deletado dessa tabela caso não haja mais nenhum vínculo com algum cliente.


| Atributos          | Constraint |           Relacionamento           |                         Descrição                         |
| ------------------ | :--------: | :--------------------------------: | :-------------------------------------------------------: |
| id_cartao          |     PK     |                 -                  |                      Chave primária                       |
| numero_cartao      |   UNIQUE   |                 -                  |   Número do cartão (Últimos 4 dígitos e criptografado)    |
| nome_cartao        |     -      |                 -                  |         Nome impresso no cartão  (Criptografado)          |
| data_expiracao     |     -      |                 -                  |        Data de expiração do cartão (Criptografado)        |
| id_cartao_bandeira |     FK     | cartao_bandeira.id_cartao_bandeira |  Chave estrangeira, faz referência a bandeira do cartão   |
| salt               |   UNIQUE   |                 -                  | Dado aleatório, usado na criptografia dos dados do cartão |

- ### cliente_tem_cartao:

A entidade '_cliente_tem_cartao_' é fruto de um relacionamento N para M entre as entidades '_cliente_' e '_cartao_', um cliente pode ter vários cartões salvos, e um cartão pode estar salvo em várias contas. 

  - Quando um cliente deleta um cartão salvo em sua conta o vínculo será desfeito fazendo a deleção do registro dessa tabela.

| Atributos  | Constraint |   Relacionamento   |                           Descrição                           |
| ---------- | :--------: | :----------------: | :-----------------------------------------------------------: |
| id_cliente |  PK / FK   | cliente.id_cliente | Chave primária / Chave estrangeira, faz referência ao cliente |
| id_cartao  |  PK / FK   |  cartao.id_cartao  | Chave primária / Chave estrangeira, faz referência ao cartão  |

- ### pagamento:

A entidade '_pagamento_' guarda o registro de todos os pagamentos relacionados aos pedidos feitos no e-commerce.

| Atributos                 | Constraint |          Relacionamento          |                        Descrição                        |
| ------------------------- | :--------: | :------------------------------: | :-----------------------------------------------------: |
| id_pagamento              |     PK     |                -                 |                     Chave primária                      |
| id_pagamento_tipo         |     FK     | pagamento_tipo.id_pagamento_tipo | Chave estrangeira, faz referência ao tipo do pagamento  |
| parcelado                 |     -      |                -                 |           Indica se o pagamento foi parcelado           |
| quantidade_parcelas       |     -      |                -                 | Quantidade de parcelas, caso o pagamento seja parcelado |
| codigo_boleto             |   UNIQUE   |                -                 |   Código do boleto, caso o pagamento seja via boleto    |
| pagamento_confirmado      |     -      |                -                 |          Indica se o pagamento foi confirmado           |
| data_pagamento_confirmado |     -      |                -                 |            Data de confirmação do pagamento             |

- ### pedido:

A entidade '_pedido_' guarda o registro de todos os pedidos feitos no e-commerce.

| Atributos             | Constraint |         Relacionamento         |                        Descrição                         |
| --------------------- | :--------: | :----------------------------: | :------------------------------------------------------: |
| id_pedido             |     PK     |               -                |                      Chave primária                      |
| id_pedido_status      |     FK     | pedido_status.id_pedido_status |  Chave estrangeira, faz referência ao status do pedido   |
| data_pedido_realizado |     -      |               -                |               Data de realização do pedido               |
| id_cliente            |     FK     |       cliente.id_cliente       |       Chave estrangeira, faz referência ao cliente       |
| id_pagamento          |     FK     |     pagamento.id_pagamento     | Chave estrangeira, faz referência ao pagamento do pedido |
| id_entrega            |     FK     |       entrega.id_entrega       |  Chave estrangeira, faz referência a entrega do pedido   |
| pedido_concluido      |     -      |               -                |             Indica se o pedido foi concluído             |
| data_pedido_concluido |     -      |               -                |               Data de conclusão do pedido                |

- ### entrega:

A entidade '_entrega_' guarda o registro de todos as entregas relacionadas aos pedidos feitos no e-commerce.

  - Uma entrega pode ter um endereço e um telefone específico, diferentes dos quais o cliente tem salvo em sua conta.
  - Informar um telefone específico para e entrega é opcional.
  - Caso não seja informado um endereço específico para e entrega, o cliente pode usar um dos seus endereços salvos.

| Atributos             | Constraint |          Relacionamento          |                                Descrição                                 |
| --------------------- | :--------: | :------------------------------: | :----------------------------------------------------------------------: |
| id_entrega            |     PK     |                -                 |                              Chave primária                              |
| id_entrega_status     |     FK     | entrega_status.id_entrega_status |          Chave estrangeira, faz referência ao status da entrega          |
| data_entrega_inicio   |     -      |                -                 |                        Data de início da entrega                         |
| valor_frete           |     -      |                -                 |                              Valor do frete                              |
| codigo_rastreio       |   UNIQUE   |                -                 |                      Código de rastreio da entrega                       |
| id_endereco           |     FK     |       endereco.id_endereco       |         Chave estrangeira, faz referência ao endereço da entrega         |
| id_transportadora     |     FK     | transportadora.id_transportadora | Chave estrangeira, faz referência a transportadora relacionada a entrega |
| id_telefone           |     FK     |       telefone.id_telefone       |         Chave estrangeira, faz referência ao telefone da entrega         |
| data_entrega_previsao |     -      |                -                 |                       Data de previsão da entrega                        |
| entrega_concluida     |     -      |                -                 |                    Indica se a entrega foi concluída                     |
| data_entrega_fim      |     -      |                -                 |                        Data de entrega do pedido                         |

- ### endereco:

A entidade '_endereco_' guarda o registro de todos os endereços relacionados aos clientes, empresas e pedidos do e-commerce.

| Atributos   | Constraint |  Relacionamento  |                             Descrição                              |
| ----------- | :--------: | :--------------: | :----------------------------------------------------------------: |
| id_endereco |     PK     |        -         |                           Chave primária                           |
| cep         |     -      |        -         |                                CEP                                 |
| logradouro  |     -      |        -         |                             Logradouro                             |
| numero      |     -      |        -         |                               Número                               |
| complemento |     -      |        -         |                            Complemento                             |
| bairro      |     -      |        -         |                               Bairro                               |
| id_cidade   |     FK     | cidade.id_cidade | Chave estrangeira, faz referência a cidade relacionada ao endereço |

- ### cliente_tem_endereco:

A entidade '_cliente_tem_endereco_' é fruto de um relacionamento N para M entre as entidades '_cliente_' e '_endereco_', um cliente pode ter vários endereços salvos, e um endereço pode estar salvo em várias contas. 

  - Quando um cliente deleta um endereço salvo de sua conta, deletemos o registro dessa tabela, ao menos que seja o endereço de criação da conta, que é desativado (quando a conta também é) ao invés de ser deletado.

| Atributos        | Constraint |    Relacionamento    |                              Descrição                               |
| ---------------- | :--------: | :------------------: | :------------------------------------------------------------------: |
| id_cliente       |  PK / FK   |  cliente.id_cliente  |    Chave primária / Chave estrangeira, faz referência ao cliente     |
| id_endereco      |  PK / FK   | endereco.id_endereco |    Chave primária / Chave estrangeira, faz referência ao endereço    |
| endereco_criacao |     -      |          -           |              Indica se é o endereço de criação da conta              |
| padrao           |     -      |          -           | Indica se é o endereço padrão do cliente para ser usado nas entregas |
| data_criacao     |     -      |          -           |         Data de criação do vínculo entre cliente e endereço          |
| desativado       |     -      |          -           |  Indica se o endereço do cliente está desativado (conta desativada)  |

- ### telefone:

A entidade '_telefone_' guarda o registro de todos os telefones relacionados aos clientes, empresas e pedidos do e-commerce.

| Atributos   | Constraint | Relacionamento |                      Descrição                       |
| ----------- | :--------: | :------------: | :--------------------------------------------------: |
| id_telefone |     PK     |       -        |                    Chave primária                    |
| telefone    |   UNIQUE   |       -        |                  Número do telefone                  |
| id_ddd      |     FK     |   ddd.id_ddd   | Chave estrangeira, faz referência ao ddd do telefone |

-> [Retornar ao índice](#índice)

### Informações:

- ### cartao_bandeira:

A entidade '_cartao_bandeira_' guarda o registro de todos as bandeiras de cartões disponíveis no e-commerce.

| Atributos          | Constraint | Relacionamento |                                Descrição                                |
| ------------------ | :--------: | :------------: | :---------------------------------------------------------------------: |
| id_cartao_bandeira |     PK     |       -        |                             Chave primária                              |
| data_criacao       |     -      |       -        | Data de criação (data em que o e-commerce começou a aceitar a bandeira) |
| bandeira           |     -      |       -        |                       Nome da bandeira do cartão                        |
| desativado         |     -      |       -        |                  Indica se a bandeira está desativada                   |
| data_desativacao   |     -      |       -        |                  Data em que a bandeira foi desativada                  |

- ### pagamento_tipo:

A entidade '_pagamento_tipo_' guarda o registro de todos os tipos de pagamentos disponíveis no e-commerce.

| Atributos         | Constraint | Relacionamento |                                    Descrição                                     |
| ----------------- | :--------: | :------------: | :------------------------------------------------------------------------------: |
| id_pagamento_tipo |     PK     |       -        |                                  Chave primária                                  |
| data_criacao      |     -      |       -        | Data de criação (data em que o e-commerce começou a aceitar o tipo de pagamento) |
| tipo_pagamento    |     -      |       -        |                            Nome do tipo de pagamento                             |
| desativado        |     -      |       -        |                  Indica se o tipo de pagamento está desativado                   |
| data_desativacao  |     -      |       -        |                  Data em que o tipo de pagamento foi desativado                  |

- ### cartao_bandeira_tem_pagamento_tipo:

A entidade '_cartao_bandeira_tem_pagamento_tipo_' é fruto de um relacionamento N para M entre as entidades '_cartao_bandeira_' e '_pagamento_tipo_', uma bandeira de cartão pode ter dois tipos de forma de pagamento (débito e crédito), e essas duas formas de pagamento podem estar relacionadas a mais de uma bandeira de cartão.

| Atributos          | Constraint | Relacionamento |                                Descrição                                |
| ------------------ | :--------: | :------------: | :---------------------------------------------------------------------: |
| id_cartao_bandeira |  PK / FK   |       -        | Chave primária / Chave estrangeira, faz referência a bandeira do cartão |
| id_pagamento_tipo  |  PK / FK   |       -        | Chave primária / Chave estrangeira, faz referência ao tipo de pagamento |

- ### pedido_status:

A entidade '_pedido_status_' guarda o registro de todos os status relacionados aos pedidos.

| Atributos        | Constraint | Relacionamento |              Descrição              |
| ---------------- | :--------: | :------------: | :---------------------------------: |
| id_pedido_status |     PK     |       -        |           Chave primária            |
| data_criacao     |     -      |       -        |      Data de criação do status      |
| status_pedido    |     -      |       -        |    Descrição do status do pedido    |
| desativado       |     -      |       -        | Indica se o status está desativado  |
| data_desativacao |     -      |       -        | Data em que o status foi desativado |

- ### transportadora:

A entidade '_transportadora_' guarda o registro de todos as transportadoras do e-commerce, quando uma transportadora para de trabalhar com o e-commerce desativamos o seu registro.

| Atributos         | Constraint |   Relacionamento   |                                            Descrição                                            |
| ----------------- | :--------: | :----------------: | :---------------------------------------------------------------------------------------------: |
| id_transportadora |     PK     |         -          |                                         Chave primária                                          |
| data_criacao      |     -      |         -          | Data de criação do registro (data em que a transportadora começou a trabalhar com o e-commerce) |
| id_unidade        |     FK     | unidade.id_unidade |                  Chave estrangeira, faz referência a unidade da transportadora                  |
| desativado        |     -      |         -          |     Indica se a transportadora está desativada (não presta mais serviços para o e-commerce)     |
| data_desativacao  |     -      |         -          |                           Data em que a transportadora foi desativada                           |

- ### entrega_status:

A entidade '_entrega_status_' faz referência aos status dos entregas do e-commerce.

| Atributos         | Constraint | Relacionamento |              Descrição              |
| ----------------- | :--------: | :------------: | :---------------------------------: |
| id_entrega_status |     PK     |       -        |           Chave primária            |
| data_criacao      |     -      |       -        |      Data de criação do status      |
| status_entrega    |     -      |       -        |   Descrição do status da entrega    |
| desativado        |     -      |       -        | Indica se o status está desativado  |
| data_desativacao  |     -      |       -        | Data em que o status foi desativado |

- ### cidade:

A entidade '_cidade_' guarda o registro de todos as cidades relacionadas aos endereços.

| Atributos | Constraint |  Relacionamento  |                       Descrição                       |
| --------- | :--------: | :--------------: | :---------------------------------------------------: |
| id_cidade |     PK     |        -         |                    Chave primária                     |
| cidade    |     -      |        -         |                    Nome da cidade                     |
| id_estado |     FK     | estado.id_estado | Chave estrangeira, faz referência ao estado da cidade |

- ### estado:

A entidade '_estado_' guarda o registro de todos os estados relacionadas as cidades.

| Atributos | Constraint | Relacionamento |   Descrição    |
| --------- | :--------: | :------------: | :------------: |
| id_estado |     PK     |       -        | Chave primária |
| estado    |     -      |       -        | Nome do estado |
| uf        |     -      |       -        |       UF       |

- ### ddd:

A entidade '_ddd_' guarda o registro de todos os ddd's relacionadas aos telefones.

| Atributos | Constraint | Relacionamento |   Descrição    |
| --------- | :--------: | :------------: | :------------: |
| id_ddd    |     PK     |       -        | Chave primária |
| ddd       |     -      |       -        |      DDD       |

-> [Retornar ao índice](#índice)

### Empresa:

- ### empresa:

A entidade '_empresa_' guarda todas as empresas (transportadoras/fornecedoras) cadastradas no e-commerce, essas empresas podem ter mais de uma unidade, que podem ser fornecedoras ou transportadoras, ou ambas. 

- Não deletamos o registro de uma empresa, fazemos a sua desativação, isso quando todas suas unidades também estão desativadas.

| Atributos        | Constraint | Relacionamento |                                        Descrição                                         |
| ---------------- | :--------: | :------------: | :--------------------------------------------------------------------------------------: |
| id_empresa       |     PK     |       -        |                                      Chave primária                                      |
| data_criacao     |     -      |       -        | Data de criação do registro (data em que a empresa começou a trabalhar com o e-commerce) |
| nome             |     -      |       -        |                                     Nome da empresa                                      |
| desativado       |     -      |       -        |                           Indica se a empresa está desativada                            |
| data_desativacao |     -      |       -        |                           Data em que a empresa foi desativada                           |

- ### unidade:

A entidade '_unidade_' guarda todas as unidades de uma empresa (transportadora/fornecedora) cadastrada no e-commerce, uma unidade pode atuar como fornecedora ou transportadora, ou ambas. 

- Não deletamos o registro de uma unidade, fazemos a sua desativação, isso quando ela também estiver desativada nas entidades '_transportadora_' e/ou '_fornecedora_'.

| Atributos        | Constraint |    Relacionamento    |                                        Descrição                                         |
| ---------------- | :--------: | :------------------: | :--------------------------------------------------------------------------------------: |
| id_unidade       |     PK     |          -           |                                      Chave primária                                      |
| id_empresa       |     FK     |  empresa.id_empresa  |            Chave estrangeira, faz referência a empresa que a unidade pertence            |
| data_criacao     |     -      |          -           | Data de criação do registro (data em que a unidade começou a trabalhar com o e-commerce) |
| razao_social     |     -      |          -           |                                 Razão social da unidade                                  |
| nome_fantasia    |     -      |          -           |                                 Nome fantasia da unidade                                 |
| cnpj             |     -      |          -           |                                     CNPJ da unidade                                      |
| id_telefone      |     FK     | telefone.id_telefone |                 Chave estrangeira, faz referência ao telefone da unidade                 |
| id_endereco      |     FK     | endereco.id_endereco |                 Chave estrangeira, faz referência ao endereço da unidade                 |
| desativado       |     -      |          -           |                           Indica se a unidade está desativada                            |
| data_desativacao |     -      |          -           |                           Data em que a unidade foi desativada                           |

-> [Retornar ao índice](#índice)

## Arquivos:

- [.gitignore](https://github.com/bccalegari/mysql_e-commerce_database/blob/main/.gitignore) -> especificação de quais arquivos devem ser ignorados pelo git.
- [CHANGELOG.md](https://github.com/bccalegari/mysql_e-commerce_database/blob/main/CHANGELOG.md) -> registro detalhado do versionamento do projeto.
- [README.md](https://github.com/bccalegari/mysql_e-commerce_database/blob/main/README.md) -> documentação do projeto.
- [e_commerce_model.mwb](https://github.com/bccalegari/mysql_e-commerce_database/blob/main/e_commerce_model.mwb) -> modelo eer do e-commerce (workbench).
- [e_commerce_model.pdf](https://github.com/bccalegari/mysql_e-commerce_database/blob/main/e_commerce_model.pdf) -> modelo eer do e-commerce (pdf).

## Tecnologias Utilizadas:

| Tecnologia | Versão |
| ---------- | ------ |
| MySQL      | 8.0    |

-> [Retornar ao índice](#índice)