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

- @Configuration: Sinaliza que a classe logo abaixo da anotação define configurações do Spring e pode registrar novos beans.

- @EnableAutoConfiguration: Ativa a "mágica" do Spring Boot. Com base nas dependências encontradas no seu pom.xml (ex: Tomcat embutido, Jackson), o Spring configura automaticamente o ambiente web, portas, conversores JSON, etc. (Habilita configurações Spring automaticamente)

- @ComponentScan: Instrui o Spring a varrer o pacote atual (start) e todos os seus subpacotes (start.controllers, start.model, etc.), procurando classes anotadas para registrá-las como componentes gerenciados no IoC Container.

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
    - Quando executado, o código prepara o app para receber requisições ou realizar tarefas dependendo do projeto ou dependências configuradas

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

### O que ocorre quando o método é executado:

1. O Spring acorda o IoC Container (ApplicationContext): Ele cria a fábrica em memória responsável por gerenciar toda a aplicação.

2. Ativação da Varredura (@ComponentScan): A anotação @SpringBootApplication contém embutida a instrução de escanear o pacote raiz (start) e todas as subpastas (start.controllers, start.model).

3. Leitura via Reflection (Reflexão Computacional):

- O Java tem um recurso nativo chamado Reflection API, que permite que um programa analise a si mesmo em tempo de execução.
- O Spring percorre os arquivos compilados (.class) e pergunta à JVM: "Essa classe GreetingController possui a anotação @RestController nela?".  
  Se a resposta for sim, o Spring executa uma série de ações automáticas:
    - Ele chama internamente new GreetingController() (instancia o objeto).
    - Guarda essa instância no depósito central (registra como um Bean gerenciado).
    - Examina os métodos internos. Ao ver @RequestMapping("/greeting"), ele cadastra a rota /greeting no DispatcherServlet para que, sempre que chegar uma chamada na porta 8080, o método greeting() seja invocado.

4. Subida do Servidor (Tomcat): O Spring inicializa a porta de rede 8080 para começar a escutar as requisições HTTP locais.

Se você apagar a anotação @RestController de cima da classe GreetingController, o Spring simplesmente passa direto por ela durante a varredura, nenhuma rota é registrada e, ao tentar acessar /greeting no navegador, você receberá um erro 404 Not Found.

### Essa é a estrutura básica que simplifica o processo de desenvolvimento, eliminando várias configurações manuais complexas feitas pelos módulos nativos do Java

---

## Classe em /controllers

GreetingController.java

### Anotação '@RestController'

---

Define que a classe é um controlador web voltado para APIs REST.  
Ela também é uma meta-anotação que combina:

- @Controller: Registra a classe como um componente Spring especializado em capturar e responder requisições HTTP (padrão MVC).
- @ResponseBody: Informa ao Spring que o valor retornado pelos métodos não deve ser interpretado como o nome de uma página HTML (View), mas sim escrito diretamente no corpo da resposta HTTP (Payload).

Por baixo dos panos: Quando o método retorna Greeting, o Spring aciona conversores de mensagem HTTP (HttpMessageConverter, usando por padrão a biblioteca Jackson) e serializa automaticamente o record Greeting(long id, String content) em um JSON puro:

#### Serialização (Java $\rightarrow$ JSON):

Quando o seu método em um @RestController retorna new Greeting(1, "Hello, Andre!"), o Jackson lê os atributos do seu record 'Greeting' e gera a string de texto

```json
{ "id": 1, "content": "Hello, Andre!" }
```

#### Desserialização (JSON $\rightarrow$ Java)

Quando um cliente envia um JSON no corpo de um POST/PUT, o Jackson lê o texto puro e instancia automaticamente a classe Java equivalente para ser usada no seu código.

### Anotação '@RequestMapping'

---

Mapeia a URL (rota) da requisição para um método específico.
No código:

- @RequestMapping("/greeting") faz com que requisições que cheguem em _.../greeting_ sejam direcionadas para o método greeting().

- Ponto de atenção & Boas Práticas: O @RequestMapping genérico atende **qualquer** verbo HTTP (GET, POST, PUT, DELETE, etc.) a menos que você especifique method = RequestMethod.GET. No mercado e nas versões modernas do Spring Boot, evita-se o uso genérico nos métodos; a recomendação expressa é utilizar as anotações semânticas dedicadas (ex. @GetMapping)

### Anotação '@RequestParam'

---

Captura parâmetros de consulta (Query Params) transmitidos na URL após o símbolo ?.

- @RequestParam(value = "name", defaultValue = "World") String name
    - value = "name": Diz ao Spring para procurar por ?name=... na requisição.
      Se você acessar http://localhost:8080/greeting?name=Andre, a variável name receberá "Andre".
    - defaultValue = "World": Se o cliente acessar apenas /greeting sem passar parâmetro, o Spring assume "World" como valor padrão em vez de deixar nulo ou retornar erro 400 (Bad Request).

---

### o que e um BEAN?

---

Um Bean é simplesmente um objeto Java comum (instância de uma classe) que é instanciado, configurado, conectado e totalmente gerenciado pelo container do Spring (o chamado IoC Container — Inversion of Control).  
Analogia da "Fábrica": Sem Spring: Você é o operário que precisa construir cada ferramenta com as próprias mãos sempre que vai usá-la.  
Com Spring: O Spring é uma fábrica automatizada. Você anota uma classe com @Component, @Service ou @RestController, e a fábrica fabrica o objeto, guarda em um depósito e o entrega pronto para quem pedir (Injeção de Dependência).  
Ciclo de vida: O Spring cria o Bean quando a aplicação sobe, injeta as dependências dele, monitora sua execução e o destrói de forma graciosa quando a aplicação é finalizada. Por padrão, no Spring, todo Bean é um Singleton (existe apenas uma única instância daquela classe compartilhada por toda a aplicação).

### Objeto `AtomicLong` no Spring

---

- **O que é:** Classe do pacote `java.util.concurrent.atomic` que armazena um valor `long` manipulável de forma atômica e segura para concorrência (_Thread-Safe_).
- **Por que é necessário no Controller:** Controllers do Spring são **Singletons** e o servidor web é **Multithread**. Múltiplas requisições acessam a mesma variável simultaneamente.
- **O perigo do `long` comum:** A operação de incremento (`++counter`) não é atômica (leitura, soma e escrita). Em concorrência, gera **Race Conditions** (valores duplicados ou perdidos).
- **Mecanismo:** Utiliza a instrução em nível de CPU chamada **CAS (Compare-And-Swap)**, evitando bloqueios pesados (_locks/synchronized_) e mantendo alta performance.
- **`incrementAndGet()`:** Incrementa a variável em 1 e devolve o novo valor instantaneamente em uma única operação indivisível.
