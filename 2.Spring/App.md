## Class Principal (App.java ou demais)

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class App {

	public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}

```

---

<br>

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
```

### Anotação '@SpringBootApplication'

---

Ponto de entrada de uma aplicação Spring Boot:

Ela é uma meta-anotação (uma anotação composta que combina várias outras em uma só).  
Suas três peças fundamentais são:   
@Configuration: Sinaliza que a classe logo abaixo da anotação define configurações do Spring e pode registrar novos beans.
@EnableAutoConfiguration: Ativa a "mágica" do Spring Boot. Com base nas dependências encontradas no seu pom.xml (ex: Tomcat embutido, Jackson), o Spring configura automaticamente o ambiente web, portas, conversores JSON, etc. (Habilita configurações Spring automaticamente)  
@ComponentScan: Instrui o Spring a varrer o pacote atual (start) e todos os seus subpacotes (start.controllers, start.model, etc.), procurando classes anotadas para registrá-las como componentes gerenciados no IoC Container.   

---

<br>

```java
public class App {

	public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}

```

### SpringApplication.run(App.class, args)

---

Inicializa o projeto no contexto de uma Aplicação Spring para a class App:

- Configura autom. os componentes Spring necessários, com bases nas dependências selecionadas para o projeto e, caso necessário, sobe um servidor virtual escolhido(padrão - Tomcat):
    - Detecta controllers, serviços, repositórios e qquer Beans necessários para rodar a aplicação;
    - Quando executado, o código prepara o app para receber requisições ou
      realizar tarefas dependendo do projeto ou dependências configuradas

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class App {

	public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}

```

### Essa é a estrutura básica que simplifica o processo de desenvolvimento, eliminando várias configurações manuais complexas feitas pelos módulos nativos do Java

