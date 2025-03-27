# Microservice and Web Engineering - 2025

***
## Repositório
| Repositório |
| --- |
| [Repositório de Conteúdo](https://github.com/cidarosa/microservice-and-web-engineering-2025) |

## Monorepo
Os projetos (API, Microsserviços) serão versionados nos monorepos de cada turma.
|Monorepo|
| --- |
| [Turma 3SIPF](https://github.com/cidarosa/microservices-hub-sipf) |
| [Turma 3SIPG](https://github.com/cidarosa/microservices-hub-sipg) |
| [Turma SIA](https://github.com/cidarosa/microservices-hub-sia) |

***
## Material de Apoio

- [Git](https://github.com/cidarosa/microservice-and-web-engineering-2025/blob/main/git/comandos-git.md)
- [Maven Dependencies](https://github.com/cidarosa/microservice-and-web-engineering-2025/tree/main/maven-dependencies)
- [Spring Profiles](https://github.com/cidarosa/microservice-and-web-engineering-2025/tree/main/profiles)


***
## Microsserviço Pagamentos
### Carga Inicial no Banco de Dados para Testes

File: `src/main/resources/import.sql`

```sql
INSERT INTO tb_pagamento(valor, nome, numero_do_cartao, validade, codigo_de_seguranca, status, pedido_id, forma_de_pagamento_id) VALUES(35.55, 'Amadeus Mozart', '6895426578961254', '12/30', '589', 'CRIADO', 5, 2);
INSERT INTO tb_pagamento(valor, nome, numero_do_cartao, validade, codigo_de_seguranca, status, pedido_id, forma_de_pagamento_id) VALUES(95.50, 'Chiquinha Gonzaga', '2457896547123654', '01/28', '389', 'CRIADO', 3, 2);
INSERT INTO tb_pagamento(valor, nome, numero_do_cartao, validade, codigo_de_seguranca, status, pedido_id, forma_de_pagamento_id) VALUES(128.0, 'Ludwig van Beethoven', '2456178921437892', '07/32', '379', 'CRIADO', 15, 2);

INSERT INTO tb_pagamento(valor,  status, pedido_id, forma_de_pagamento_id) VALUES(1200, 'CRIADO', 4, 1);
INSERT INTO tb_pagamento(valor,  status, pedido_id, forma_de_pagamento_id) VALUES(1200, 'CANCELADO', 4, 1);
INSERT INTO tb_pagamento(valor,  status, pedido_id, forma_de_pagamento_id) VALUES(125.25, 'CONFIRMADO', 6, 1);
```


***
## Links


