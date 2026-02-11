# Relatório do Dia 2

## O que foi feito
### O Coração do Rust (Ownership & Borrowing)
#### Conceito: Pilha vs Heap, Semântica de movimentação, Referências (&), Referências mutáveis (&mut).
O que foi estudado (https://doc.rust-lang.org/book/):
 - Capítulo 4.1: O que é Propriedade? (foco em Stack vs Heap e Move semantics).
 - Capítulo 4.2: Referências e Empréstimos (regras de & e &mut).

## Exercicio atualizador do saldo
  Crie uma variável mutável de saldo (f64). Passe uma referência mutável (&mut f64) para uma função aplicar_taxa que deduz 10% do valor. Imprima o saldo atualizado no main.
### Solucao 1: Usando o retorno
```
fn main(){
  let mut saldo: f64 = 400.0;

  // Aplicando conceito de 'borrowing' e emprestando a variavel (por meio de ponteiro) para a funcao
  saldo = aplicar_taxa(&saldo);
  println!("{}", saldo);
}

// A funcao RETORNA o valor. i.g., o valor de saldo nao eh alterado.
fn aplicar_taxa(saldo: &f64) -> f64{
  saldo * 0.9
}
```
Nota: Eh possivel alterar o parametro e o argumento para passar a variavel em si, e nao a referencia para ela. Isso altera o comportamento do processamento e da memoria do programa; a funcao nao vai consultar mais o endereco de memoria recebido, e sim criar uma copia no escopo local da variavel

### Solucao 2: Alterando o valor da variavel por referencia (usando ponteiro)
```
fn main(){
  let mut saldo: f64 = 400.0;
  // &mut -> envia o ponteiro de saldo, de forma que ele pode fazer alteracoes na variavel.
  aplicar_taxa(&mut saldo);
  println!("{}", saldo);
}

fn aplicar_taxa(saldo: &mut f64){
  // Imprime o valor original de saldo, que eh 400
  println!("{}", saldo);
  // Altera o valor de saldo para 90% do valor. Essa mudanca persiste mesmo apos o escopo da funcao.
  *saldo = *saldo * 0.9;
}

```

## Diferenca heap vs stack
A principal diferenca notada num contexto de array foi:
o ponteiro heap atua como um ponteiro direto para o inicio do vetor, enquanto o ponteiro na stack atua como um descrito; de forma analoga ao metadado.
```
fn main() {
  let mut saldo = String::from("100.00");
  
  // Endereço do descritor na Stack
  println!("Endereço na Stack: {:p}", &saldo);
  
  // Endereço do conteúdo real no Heap
  println!("Endereço no Heap: {:p}", saldo.as_ptr());
  
  saldo = "teste".to_string();
  
  // Endereço do descritor na Stack
  println!("Endereço na Stack: {:p}", &saldo);
  
  // Endereço do conteúdo real no Heap
  println!("Endereço no Heap: {:p}", saldo.as_ptr());
  
  saldo = "teste muito maior".to_string();
  
  // Endereço do descritor na Stack
  println!("Endereço na Stack: {:p}", &saldo);
  
  // Endereço do conteúdo real no Heap
  println!("Endereço no Heap: {:p}", saldo.as_ptr());
}
```
  No exemplo acima, o endereco do descritor eh o mesmo, independente das mudancas na variavel. Ja quando a variavel eh alterada por inteiro, o endereco do heap eh alterado tambem.
