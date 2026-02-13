# Relatório do Dia 3

## O que foi feito

### Coleções, Tratamento de Erros e Abstrações

Nesta etapa, focamos em como gerenciar múltiplos dados e como lidar com falhas de forma robusta, além de introduzir polimorfismo com Traits.

**O que foi estudado ([https://doc.rust-lang.org/book/](https://doc.rust-lang.org/book/)):**

* **Capítulo 8:** Coleções Comuns (`Vec<T>` e `HashMap<K, V>`).
* **Capítulo 9:** Tratamento de Erros (`Result` e `Option`).
* **Capítulo 10:** Tipos Genéricos e Traits (Definindo comportamentos compartilhados).

---

## 1. Desafio: O Ledger Simples (HashMap e Option)

O objetivo foi criar um sistema de saldos onde endereços (chaves) mapeiam para valores. O foco foi tratar a ausência de um registro sem causar o crash do programa (`panic`).

**Questão original: Use um `HashMap` onde a chave é o endereço (`String`) e o valor é o saldo (`u64`). Insira 3 contas. Tente buscar uma conta que não existe e trate o retorno `Option::None` de forma segura (sem usar `unwrap()`, use `match` ou `if let`).**

### 1.1 - Solução

```rust
use std::collections::HashMap;

struct Conta {
    endereco: String,
    saldo: f64,
}

fn main() {
    let mut vetor_contas = Vec::new();
    // Populando dados iniciais
    vetor_contas.push(Conta { endereco: String::from("ABC"), saldo: 6.99 });
    vetor_contas.push(Conta { endereco: String::from("GHI"), saldo: 3333.77 });

    // Criamos o HashMap guardando REFERÊNCIAS (&) para evitar clonar Strings (mais performance)
    let mut contas = HashMap::<&String, &f64>::new();
    for conta in &vetor_contas {
        contas.insert(&conta.endereco, &conta.saldo);
    }

    let conta_desejada = String::from("KKK");
    
    // .get() retorna um Option<&V>. Se a chave não existir, retorna None em vez de quebrar o código.
    match contas.get(&conta_desejada) {
        None => println!("Erro: Conta não localizada."),
        Some(saldo) => println!("Saldo de {}: {}", conta_desejada, saldo),
    }
}

```

---

## 2. Desafio: Simulador de Pagamento (Result e Enums)

Implementamos uma lógica de validação de pagamento que retorna um `Result`. Em vez de apenas strings, usamos um `enum` para categorizar erros de forma estruturada.


**Questão original: Crie uma função `enviar_pagamento(valor: u64)` que retorna `Result<String, String>`. Se o valor for > 100, retorne `Ok("Enviado")`. Se for menor, retorne `Err("Valor muito baixo")`. Trate o erro no `main`.**

### 2.1 - Solução

```rust
enum Erros {
    SaldoMaior, // Ex: Valor acima do limite permitido (900)
    SaldoMenor  // Ex: Valor abaixo do mínimo (100)
}

fn enviar_pagamento(valor: u64) -> Result<String, Erros> {
    if valor > 100 && valor <= 900 {
        Ok("Pagamento Enviado com Sucesso".to_string())
    } else if valor > 900 {
        Err(Erros::SaldoMaior)
    } else {
        Err(Erros::SaldoMenor)
    }
}

fn main() {
    let valor_pagamento = 9000;
    let resultado = enviar_pagamento(valor_pagamento);

    match resultado {
        Ok(msg) => println!("Sucesso: {}", msg),
        Err(tipo_erro) => {
            match tipo_erro {
                Erros::SaldoMaior => println!("Erro: O valor excede o limite de segurança."),
                Erros::SaldoMenor => println!("Erro: O valor é insuficiente para a taxa mínima."),
            }
        }
    }
}

```

---

## 3. Desafio: O Formatador Universal (Traits e Display)

Exploramos como implementar comportamentos customizados para nossas próprias structs usando a trait `std::fmt::Display`, permitindo o uso direto no `println!()`.

**Questão original: Implemente a trait `std::fmt::Display` para a struct `Block` da semana passada, para que você possa dar `println!("{}", bloco)` diretamente, sem chamar métodos auxiliares.**

### 3.1 - Solução

```rust
use std::fmt;

struct Carro {
    cor: String
}

struct Caminhao {
    cor: String,
    cacamba: String,
}

// Implementando o comportamento de exibição para Carro
impl fmt::Display for Carro {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "Veículo: Carro | Cor: {}", self.cor)
    }
}

// Implementando o comportamento de exibição para Caminhao
impl fmt::Display for Caminhao {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "Veículo: Caminhão | Cor: {} | Caçamba: {}", self.cor, self.cacamba)
    }
}

fn main() {
    let carro_verde = Carro { cor: "Verde".to_string() };
    let caminhao = Caminhao { cor: "Verde".to_string(), cacamba: "Branca".to_string() };

    // Agora podemos imprimir diretamente sem métodos auxiliares
    println!("{}", carro_verde);
    println!("{}", caminhao);
}

```

## Observações Técnicas do Dia

* **Slices:** Pedro sugeriu o estudo de *String Slices* (`&str`) para entender como referenciar partes de uma string sem copiar os dados.
* **Segurança de Tipos:** Reforçamos que o Rust obriga o tratamento de `Option` e `Result`, impedindo erros comuns de "valor nulo" ou "exceção não capturada" em tempo de execução.
* O exercício a seguir fica como atividade para casa.
  
```
Desafio 2 (A Função Duplicadora):
Crie uma função genérica `duplicar_item<T>` que recebe um item e imprime “Processando item…”.
Tente restringir `T` para tipos que implementem a trait `Debug` (use `where T: Debug`).
```
