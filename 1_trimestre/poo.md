# Programação Orientada a Objetos (POO) com TypeScript

# O que é POO?

POO (Programação Orientada a Objetos) é um paradigma de programação baseado em objetos.

Ela busca representar elementos do mundo real através de:
- classes
- objetos
- atributos
- métodos

Seus principais objetivos são:
- organização
- reutilização de código
- manutenção
- segurança

---

# Classes

Classes são moldes/modelos usados para criar objetos

Elas definem:
- atributos
- métodos
- comportamentos

---

# Objetos

Objetos são instâncias de classes

Representam dados concretos armazenados na memória

---

# Variáveis VS Atributos

## Variáveis

Espaços reservados na memória para armazenar dados

---

## Atributos

São variáveis que pertencem às classes/objetos

---

# Funções VS Métodos

## Funções

Blocos reutilizáveis de código

---

## Métodos

Métodos são funções que pertencem às classes/objetos

---

# Construtores

Métodos especiais responsáveis por inicializar os atributos do objeto

Executam automaticamente quando o objeto é criado

---

# This

`this` é uma keyword que representa a própria instância da classe

Ela referencia o próprio objeto

---

# Getters e Setters

## Getter

Método especial que retorna um atributo do objeto

---

## Setter

Método especial que altera um atributo do objeto

---

# Encapsulamento

Pilar da POO responsável por proteger os dados da classe contra acessos indevidos

Objetivos:
- evitar interferência externa
- controlar acessos
- aumentar segurança

---

# Modificadores de acesso

## Public

Pode ser acessado de qualquer lugar do programa

---

## Private

Só pode ser acessado dentro da própria classe

---

## Protected

Pode ser acessado pela própria classe e pelas classes filhas

---

# Métodos e atributos estáticos (static)

Membros pertencentes à própria classe, e não aos objetos

Não precisam de instância

---

# Herança

Permite que uma classe filha herde atributos e métodos da classe pai

Objetivo: Reutilização de código

---

# Super

`super` permite acessar elementos da classe pai

Utilizado em construtores

---

# Exemplo completo

```ts
class Pessoa {
    // ATRIBUTOS
    private nome: string
    private idade: number

    static especie: string = "Humano" // ATRIBUTO ESTÁTICO

    // CONSTRUTOR
    constructor (nome: string, idade: number){
        // THIS
        this.nome = nome
        this.idade = idade
    }

    // GETTER
    getNome(): string {
        return this.nome
    }

    // SETTER
    setNome (nome: string) {
        this.nome = nome
    }

    // MÉTODO
    apresentar() {
        console.log(`Meu nome é ${this.nome} e tenho ${this.idade} anos)
    }
}

// HERANÇA
class Aluno extends Pessoa {
    curso: string

    constructor (
        nome: string,
        idade: number,
        curso: string
    ) {
        super(nome, idade) // SUPER
        this.curso = curso
    }
}

// OBJETO
const aluno1 = new Aluno (
    "Vinicius",
    17,
    "Informática"
)

aluno1.apresentar() // MÉTODO

console.log(aluno1.getNome()) // GETTER

aluno1.setNome("Carlos") // SETTER

console.log(Pessoa.especie) // STATIC
```

---

# Resumo rápido

| Conceito | Definição |
|---|---|
| Classe | Molde de objetos |
| Objeto | Instância da classe |
| Atributo | Variável da classe |
| Método | Função da classe |
| Constructor | Inicializa objeto |
| Getter | Retorna atributo |
| Setter | Modifica atributo |
| Public | Acesso livre |
| Private | Acesso interno |
| Protected | Acesso pai/filhos |
| Static | Pertence à classe |
| Herança | Reutilização |
| This | Próprio objeto |
| Super | Acesso à classe pai |
