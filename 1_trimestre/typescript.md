# TypeScript Essencial

## O que é?

TypeScript é JavaScript com tipagem.

## Tipos básicos

```ts
let nome: string = "Vinicius"
let idade: number = 17
let ativo: boolean = true
```

## Arrays

```ts
let numeros: number[] = [1,2,3]
```

## Union Type

```ts
let id: string | number
```

## Funções

```ts
function soma(a:number, b:number): number {
    return a + b
}
```

## Interface

```ts
interface Usuario {
    nome: string
    idade: number
}
```

## Type

```ts
type Produto = {
    nome: string
    preco: number
}
```

## Generics

```ts
function identidade<T>(valor:T): T {
    return valor
}
```

## Any

Evite usar:

```ts
let valor:any
```

## Diferença entre JS e TS

| JS | TS |
|---|---|
| Sem tipagem | Com tipagem |
| Mais flexível | Mais seguro |
| Mais simples | Melhor manutenção |
