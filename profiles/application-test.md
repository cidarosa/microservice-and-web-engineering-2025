# Spring Profiles
## Application Properties
### Profiles
##### Profile Test DB H2

Path: `src/main/resources/`

File name: `application-test.properties`

```properties
# para expor o trace - somente para testes
# server.error.include-stacktrace = always
server.error.include-message=always

# para não expor o trace
server.error.include-stacktrace = never
# server.error.include-message=never

# Conexão com o banco H2
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
spring.datasource.password=

# H2 Client
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

# JPA, SQL
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.defer-datasource-initialization=true
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

# http://localhost:8080/h2-console/
```