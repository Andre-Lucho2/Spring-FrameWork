## Maven

- o Maven tem como uma de suas responsabilidades gerir dependências, plugins, repositórios

- os pacotes com as dependências ficam armazenadas na pasta ~/.m2/repository/

- As mesmas dependências podem ser reutilizadas em outros projetos

### Gerência:

Após a checagem, ele instala as dependências de acordo com a versão do Spring Boot do projeto
--> gerando um arquivo .jar na pasta 'target'

## Arquivo pom.xml

### Tags:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>

<project> --> localização e versão do Maven utilizada
```

O Projeto em Java e/ou Spring é dividido em módulos, cada um com a sua responsabilidade
<parent> --> É o módulo Pai --> a partir deles, os módulos filho <childs> herdam dependências, plugins, repositórios...
Evitando a duplicação e falta de sincronia de versão de dependências

```xml
<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>3.4.0</version>
		<relativePath/> <!-- lookup parent from repository -->
</parent>
```

### tag 'parent' Acima:

As dependências, plugins, ferramentas, etc. do Projeto estão sendo herdandas de 'spring-boot-parent' para a versão 3.4.0
Elas serão compatíveis com essa versão Spring específica(3.4.0)

```xml
    <groupId>com.example</groupId> --> pacote raiz do projeto
	<artifactId>start</artifactId> --> nome do projeto
	<version>0.0.1-SNAPSHOT</version> --> versão

    OBS. artifactId + version == nome do arquivo .jar

	<name><name/> --> nome do projeto

	<properties> --> versão Java + bibliotecas
		<java.version>21</java.version>
	</properties>
	<dependencies> --> dependências do projeto
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<scope>runtime</scope>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build> --> plugins
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
        <repositories> --> repositórios alternativos para baixar as dependências
            <repository>
            </repository>
        </repositories>
	</build>
```

Quando temos alguma incompatibilidade entre versões de bibliotecas compartilhadas em uma feramente e o Spring, utilizamos a tag **exclusions**
<img src='./2.maven/exclusion.png'>
