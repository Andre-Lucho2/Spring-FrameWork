## MVC

O **MVC (Model-View-Controller)** é um padrão de projeto arquitetural cujo objetivo principal é **separar as responsabilidades** de uma aplicação em três camadas distintas: como os dados são estruturados, como eles são apresentados ao usuário e quem orquestra a comunicação entre eles.

---

### 1. A Analogia do Restaurante

Para visualizar o fluxo com clareza, pense no funcionamento de um restaurante:

- **View (O Cliente / Cardápio / Mesa):** É com quem o cliente interage. É a interface visual onde o prato é servido ou onde a resposta é entregue.
- **Controller (O Garçom):** É quem atende a mesa. Ele recebe o pedido do cliente, entende o que foi pedido, leva o comando para a cozinha e, quando a comida fica pronta, entrega de volta na mesa. O garçom **não cozinha**; ele apenas orquestra o fluxo.
- **Model (A Cozinha / Despensa / Receitas):** Onde estão as regras de preparo, os ingredientes e os dados. A cozinha processa o alimento, aplica as regras culinárias e devolve o prato pronto para o garçom.

---

### 2. Os Três Componentes no Detalhe Técnico

```
        Requisição HTTP (GET /greeting)
Cliente ---------------------------------> [ CONTROLLER ]
                                               │
                                               │ 1. Consulta regras/dados
                                               ▼
                                           [ MODEL ]
                                               │
                                               │ 2. Retorna dados brutos
                                               ▼
Cliente <--------------------------------- [ VIEW ] (ou JSON direto)
        Resposta Formatada (HTML ou JSON)

```

#### A. Model (Modelo)

- **Papel:** Representa o domínio da aplicação, as entidades de negócio e o estado dos dados.
- **O que faz:** Contém as classes de dados, validações e regras de negócio. No seu projeto, a classe `Greeting` desempenha o papel de modelo (transportando o `id` e o `content`).

- **Regra:** O Model não sabe como ele será desenhado na tela nem se importa com rotas HTTP.

#### B. View (Visão)

- **Papel:** A camada de apresentação visual dos dados.
- **O que faz:** Tradicionalmente em aplicações web clássicas (Spring MVC tradicional), a View é uma página renderizada no servidor (usando templates como Thymeleaf, JSP ou HTML). Ela pega o `Model` e o insere em tags visuais para exibição no navegador.
- **Em APIs REST modernas:** A "View" visual deixa de existir no backend. O cliente (React, Angular, aplicativo móvel) é a própria interface, e o Spring entrega apenas os dados brutos convertidos em **JSON** via `@ResponseBody` ou `@RestController`.

#### C. Controller (Controlador)

- **Papel:** O intermediário e orquestrador.
- **O que faz:** É a porta de entrada da aplicação. Ele escuta as URLs mapeadas (`@RequestMapping`, `@GetMapping`), recebe os parâmetros (`@RequestParam`, `@PathVariable`), aciona as regras de negócio do Model e devolve a resposta adequada para a View ou diretamente ao cliente.

---

### 3. Como o Spring MVC orquestra isso nos bastidores?

No ecossistema Spring Web, o padrão MVC gira em torno de um componente interno central: o **`DispatcherServlet`** (conhecido como _Front Controller_).

1. O cliente faz uma requisição HTTP para a porta `8080` (ex: `GET /greeting?name=Andre`).

2. O servidor embutido (Tomcat) entrega a chamada para o **`DispatcherServlet`**.

3. O `DispatcherServlet` consulta a tabela de rotas e encontra o seu **`GreetingController`**.

4. O método `greeting()` é executado e gera um novo **`Greeting`** (Model).

5. Como a classe está anotada com **`@RestController`** (que inclui `@ResponseBody`):

- O Spring pula a busca por um arquivo HTML (View tradicional).
- O conversor interno (Jackson) transforma o objeto `Greeting` diretamente em uma string **JSON**.

6. O `DispatcherServlet` devolve o JSON no corpo da resposta HTTP com status `200 OK`.

---

### Resumo para o seu caderno de estudos

### Padrão MVC (Model-View-Controller)

- **Definição:** Padrão arquitetural que divide o software em três camadas para separar a lógica de apresentação da lógica de negócio e do controle de fluxo.
- **Componentes:**
    - **Model:** Estruturas de dados, entidades de domínio e regras de negócio (ex: classe `Greeting`).
    - **View:** Camada visual de exibição ao usuário (HTML/Thymeleaf na web clássica, ou JSON em APIs REST).
    - **Controller:** Orquestrador que recebe requisições HTTP, extrai parâmetros e decide quem processa os dados (ex: `@RestController`).
- **Fluxo no Spring:** `Cliente` -> `DispatcherServlet` -> `Controller` -> `Model` -> `Jackson (Serialização JSON)` -> `Resposta HTTP`.
