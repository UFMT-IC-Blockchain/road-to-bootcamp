# Relatório do Dia 1

## O que foi feito
### Revisão dos tópicos a serem abordados no bootcamp
- Foram publicado os tópicos no grupo do Discord:
        
    ``` 
    Instalação
    Memória e tipos de variáveis
    Ownership e borrowing
    Struct e enums
    Collections e tratamento de erros
    Abstrações e padrões avançados
    Ponteiros inteligentes e mutabilidade interior
    ``` 

### Atividades

- Usamos a documentação do Rust para estudar os tópicos: https://doc.rust-lang.org/book/
 
    1. Getting Started
        1.1. Installation [Link Aqui](https://doc.rust-lang.org/book/ch01-01-installation.html)
        1.2. Hello, World!
        1.3. Hello, Cargo!
    2. Programming a Guessing Game
    3. Common Programming Concepts
        3.1. Variables and Mutability
        3.2. Data Types
        3.3. Functions
        3.4. Comments
        3.5. Control Flow 


- Fizemos um momento de leitura e em seguida, resolvemos alguns exercícios propostos.

## 1 - Exercício

``` 
(O Validador de ID): Crie uma função que receba um número (u32) simulando um ID de transação. Se for par, retorne “Validado”, se for ímpar, retorne “Pendente”. Use if/else inicialmente e depois refatore para usar match.
``` 

### 1.1 - Solução
``` 
use rand::Rng;

use rand::Rng;

fn main(){
    let num_rand = rand::rng().random_range(1..10);
    
    println!("{}", num_rand);
    
    let eh_par = num_rand % 2 == 0;
    
    if eh_par{
        println!("Validado");
    }
    else{
        println!("Pendente")
    }
    
    
    match eh_par{
        true => println!("Validado como par"),
        false => println!("Pendente pois é impar")
    }
}
``` 

## 2 - Exercício

``` 
(Conversor de Fee): Crie um loop que converta uma lista de valores de “Stroops” (unidade menor) para “Lumens” (dividir por 10.000.000). Se o valor for 0, pare o loop (break).
``` 
### 2.1 - Solução

``` 
fn main (){
    let a: [u32; 10] = [8, 2, 7, 200, 900, 60, 77, 333, 0, 8];

    for element in a{
        if element == 0 {
            println!("parou");
            break;
        }

        let valor : f64 = (element as f64) / 10_000_000.00;

        println!("{} strops são {} lumens", element, valor);
    }
}
``` 

## 3 - Exercício

``` 
(A Carteira Imutável): Crie uma String representando o nome de uma carteira. Passe essa variável para uma função imprimir_carteira. Tente usar a variável original depois da chamada. Erro esperado: Value used after move. Corrija passando uma referência (&String).
``` 
### 3.1 - Solução

``` 
fn imprimir_carteira(palavra: String){
    println!("{}", palavra);
}

fn main(){
    let palavra = String::from("teste");
    
    imprimir_carteira(palavra);
    
    imprimir_carteira(palavra);
}
``` 

``` 
Errors
Exited with status 101
Standard Error
   Compiling playground v0.0.1 (/playground)
error[E0382]: use of moved value: `palavra`
  --> src/main.rs:10:23
   |
 6 |     let palavra = String::from("teste");
   |         ------- move occurs because `palavra` has type `String`, which does not implement the `Copy` trait
 7 |     
 8 |     imprimir_carteira(palavra);
   |                       ------- value moved here
 9 |     
10 |     imprimir_carteira(palavra);
   |                       ^^^^^^^ value used here after move
   |
note: consider changing this parameter type in function `imprimir_carteira` to borrow instead if owning the value isn't necessary
  --> src/main.rs:1:31
   |
 1 | fn imprimir_carteira(palavra: String){
   |    -----------------          ^^^^^^ this parameter takes ownership of the value
   |    |
   |    in this function
help: consider cloning the value if the performance cost is acceptable
   |
 8 |     imprimir_carteira(palavra.clone());
   |                              ++++++++

For more information about this error, try `rustc --explain E0382`.
error: could not compile `playground` (bin "playground") due to 1 previous error
``` 

#### Após a correção

``` 
fn imprimir_carteira(palavra: &String){
    println!("{}", palavra);
}

fn main(){
    let palavra = String::from("teste");
    
    imprimir_carteira(&palavra);
    
    imprimir_carteira(&palavra);
}

``` 
``` 
Execution
Close
Standard Error
   Compiling playground v0.0.1 (/playground)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 0.49s
     Running `target/debug/playground`
Standard Output
teste
teste
``` 