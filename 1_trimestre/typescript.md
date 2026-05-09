# TypeScript

## O que é?

TypeScript é um superset de JavaScript que adiociona tipagem

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

## Funções

```ts
function soma(a:number, b:number): number {
    return a + b
}
```

## Any

Keyword que torna a tipagem não obrigatória (Utilizar com cautela)

```ts
let valor: any
```

## Diferença entre JS e TS

| JS | TS |
|---|---|
| Sem tipagem | Com tipagem |
| Mais flexível | Mais seguro |
| Mais simples | Melhor manutenção |
