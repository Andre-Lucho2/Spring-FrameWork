A funcionalidade de **`record`** foi introduzida de forma definitiva no **Java 16** (com versões prévias no Java 14 e 15).

Trata-se de um tipo especial de classe cujo objetivo principal é ser um **transportador de dados imutável** (*transparent carrier for immutable data*), eliminando grande parte do código repetitivo (*boilerplate*) tradicional da linguagem.

---

### 1. O Problema que o `record` resolve

No Java tradicional (padrão *JavaBean*), se você precisa de uma classe simples apenas para carregar dados — como `Invoice` —, você precisaria escrever:

* Atributos `private final`;


* Construtor com todos os argumentos;


* Métodos *getters* para cada atributo;


* `equals()` e `hashCode()` (para comparar dois objetos pelos seus valores e usá-los em coleções);


* `toString()` legível (para não imprimir o endereço de memória tipo `Invoice@7ad041f3`).



Isso gerava facilmente 40 a 50 linhas de código para representar apenas 2 campos.

---

### 2. A Solução com `record`

Com o `record`, o compilador Java gera **tudo isso automaticamente** em apenas uma linha:

```java
package model.entities;

public record Invoice(Double basicPayment, Double tax) {
}

```

Ao compilar essa linha única, o Java entrega nos bastidores:

1. **Campos privados e finais:** `private final Double basicPayment;` e `private final Double tax;`.


2. **Construtor canônico:** Um construtor com todos os parâmetros `(Double basicPayment, Double tax)`.


3. **Métodos de leitura (Accessors):** Com o mesmo nome dos componentes, mas **sem o prefixo "get"**:
* `invoice.basicPayment()` (ao invés de `invoice.getBasicPayment()`)


* `invoice.tax()` (ao invés de `invoice.getTax()`)




4. **`toString()` pronto:** Retorna formatado legivelmente, por exemplo: `Invoice[basicPayment=50.0, tax=10.0]`.


5. **`equals()` e `hashCode()` baseados no conteúdo:** Dois records com os mesmos valores de campos serão considerados rigorosamente iguais via `.equals()`.



---

### 3. Como adicionar métodos personalizados no `record`?

O fato de ser um `record` não impede que você adicione métodos extras ou lógicas de validação.

No caso do seu projeto, onde a fatura calcula o pagamento total, você pode escrever:

```java
package model.entities;

public record Invoice(Double basicPayment, Double tax) {

    // Método de negócio extra
    public Double getTotalPayment() {
        return basicPayment + tax;
    }
}

```

E no seu `App.java`, você usaria assim:

```java
System.out.println("Pagamento básico: " + rental01.getInvoice().basicPayment());
System.out.println("Imposto: " + rental01.getInvoice().tax());
System.out.println("Pagamento total: " + rental01.getInvoice().getTotalPayment());

```

---

### 4. Construtor Compacto (Validações elegantes)

Os records possuem uma sintaxe exclusiva para validações chamada **construtor compacto**. Você não precisa reatribuir `this.x = x`:

```java
public record Invoice(Double basicPayment, Double tax) {

    // Construtor compacto para regras de integridade
    public Invoice {
        if (basicPayment < 0 || tax < 0) {
            throw new IllegalArgumentException("Valores não podem ser negativos!");
        }
    }

    public Double getTotalPayment() {
        return basicPayment + tax;
    }
}

```

---

### 5. Regras e Limitações de um `record`

Para garantir a simplicidade e a imutabilidade, o Java impõe algumas regras restritas aos records:

* **São implicitamente `final`:** Nenhuma classe pode estender (`extends`) um record, e um record não pode estender outra classe (ele já herda diretamente de `java.lang.Record`).


* **Campos são `final`:** Não existem *setters*. Depois de criado com `new Invoice(...)`, seus atributos nunca mais mudam de valor.


* **Não permite atributos de instância extras:** Você não pode declarar `private int outroCampo;` dentro do corpo do record (apenas atributos `static` são permitidos).
* **Podem implementar interfaces:** Embora não possam herdar de classes, eles podem implementar contratos (por exemplo, `implements Serializable`).



---

### 6. Quando usar `record` no mercado?

| Usar `record` | Evitar `record` (usar `class` normal) |
| --- | --- |
| **DTOs (*Data Transfer Objects*):** Dados que trafegam entre API (JSON) e backend.

 | **Entidades JPA/Hibernate:** Que exigem construtor vazio padrão e mutabilidade controlada pelo ORM.

 |
| **Value Objects (Objetos de Valor):** Como `Invoice`, `Endereco`, `Coordenadas`.

 | Classes com ciclo de vida complexo e alteração frequente de estado interno.

 |
| Retornos de consultas customizadas no banco ou agregações de múltiplos serviços.

 | Cenários em que herança de classes (`extends OutraClasse`) seja obrigatória.

 |
