# Desenvolvendo uma lista de tarefas por fazer
A famosa "TO-DO List", nessa aula vamos implementar uma aplicação para que o usuário possa criar uma lista de tarefas
com diversas funcionalidades. Vai ser um ótimo exercício para vermos as funcionalidades mais importantes do Vue.

Mas antes de começar, vamos apagar os experimentos que fizemos na última aula, antes disso recomendo fazer um commit
para poder dar uma olhada nesses seus experimentos no futuro.

## Deixe o src/App.vue dessa maneira:
```html
<template>
  <div></div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component'

@Options({})
export default class App extends Vue {}
</script>

<style lang="scss">
@import './assets/scss/app.scss';
</style>

```

## Começando pelo Modelo da lista de tarefas
Para termos uma lista de tarefas primeiro precisamos criar a classe de Typescript que representa uma única tarefa, então crie um
arquivo `Task.ts` na pasta `src/model` com o seguinte código:
```typescript
export class Task {
  text: string

  constructor (text: string) {
    this.text = text
  }
}

```
No código acima estamos:
- declarando e exportando (tornando pública) a classe de Typescript chamada `Task`
- que possui uma propriedade chamada `text` que é uma `string` que não aceita o valores nulos
- e para termos certeza que `text` sempre terá um valor não-nulo precisamos que este valor seja passado no construtor

Você me pergunta: Por que é importante que `text` não aceite null?

E eu te respondo: Por que é um pré-requisito de uma tarefa ter alguma descrição e além disso não queremos ter que
precisar ficar checando se aquele texto é nulo ou não antes de usarmos ele.

## Trabalhando com Listas
Certo, agora que definimos a tarefa podemos definir como será a lista delas, então no `<script>` do nosso `src/App.vue`
iremos escrever o seguinte:
```html
<script lang="ts">
import { Options, Vue } from 'vue-class-component'
import { Task } from '@/model/Task'

@Options({})
export default class App extends Vue {
  tasks = [
    new Task('Aprenda Javascript e CSS'),
    new Task('Aprenda Vue, Typescript e Tailwind'),
    new Task('Aprenda diversas bibliotecas que vão te ajudar'),
    new Task('Construa coisas gigantes')
  ]
}
</script>
```
Repare que importamos a `Task` e depois declaramos um array `tasks`, instanciando 4 delas.

## Exibindo Listas no Vue
Para exibir uma lista no vue basta usar um `v-for` e o atributo `key`, então modifique o `<template>` dessa forma:
```html
<template>
  <ul class="list-disc pl-6">
    <li v-for="(todo, i) in tasks" :key="i">
      {{ todo.text }}
    </li>
  </ul>
</template>
```
Usamos as classes de CSS `list-disc` e `pl-6` para darmos o estilo de lista e um padding do lado esquerdo.

Colocamos o código `v-for="(todo, i) in tasks"`, a sintaxe é bem simples: do lado esquerdo, entre parênteses você cria
uma variável com o valor de cada item da lista, e em seguida uma variável para o índice de cada item da lista, após
isso a palavra reservada `in` e por último o nome do array.

Também colocamos o `:key="i"`, isso é bem importante para o Vue controlar bem a lista sem repetir nenhum item quando o
array for modificado. Se o seu objeto na lista possuir um ID é até melhor vc colocar este ID na `key` do que o indice
como estou fazendo. Inclusive, vamos fazer isso!

## Um loop usando IDs para as `key`
Modificando a Task.ts:
```typescript
export class Task {
  id: number
  text: string

  constructor (id: number, text: string) {
    this.id = id
    this.text = text
  }
}

```
Modificando o App.vue
```html
<template>
  <ul class="list-disc pl-6">
    <li v-for="todo in tasks" :key="todo.id">
      {{ todo.text }}
    </li>
  </ul>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component'
import { Task } from '@/model/Task'

@Options({})
export default class App extends Vue {
  tasks = [
    new Task(1, 'Aprenda Javascript e CSS'),
    new Task(2, 'Aprenda Vue, Typescript e Tailwind'),
    new Task(3, 'Aprenda diversas bibliotecas que vão te ajudar'),
    new Task(4, 'Construa coisas gigantes')
  ]
}
</script>
```
Veja que já que não precisamos mais do índice nem precisamos declara-lo: `v-for="todo in tasks" :key="todo.id"`

## Composição de Componentes
Já que queremos criar várias funcionalidades de exibição e interação envolvendo a Task é interessante termos um
componente do Vue só pra ela. Isso vai ajudar a organizarmos melhor o código, separando a responsabilidade em diversos
pequenos componentes.

Então vamos lá, crie um arquivo `TaskItem.vue` na pasta `src/components` da seguinte forma: 

```html
<template>
  <div></div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component'

@Options({})
export default class TaskItem extends Vue {}
</script>

```
Repare que dessa vez não incluímos a tag `<style>`, ela não é obrigatória, mas podemos adiciona-la posteriormente caso
haja necessidade.

Agora voltando pro `App.vue` precisamos começar a usar esse componente:
```html
<template>
  <div>
    <task-item v-for="todo in tasks" :key="todo.id" />
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component'
import { Task } from '@/model/Task'
import TaskItem from '@/components/TaskItem.vue'

@Options({
  components: { TaskItem }
})
export default class App extends Vue {
  tasks = [
    new Task(1, 'Aprenda Javascript e CSS'),
    new Task(2, 'Aprenda Vue, Typescript e Tailwind'),
    new Task(3, 'Aprenda diversas bibliotecas que vão te ajudar'),
    new Task(4, 'Construa coisas gigantes')
  ]
}
</script>
```
No código acima fizemos o seguinte:
- No template, substituímos o `li` pelo nosso componente `task-item`, repare que escrevemos o _nome-do-componente-em-kebab-case_.
- Importamos o novo componente.
- Alteramos o `@Options` para declarar que vamos usar este componente.

Mas o `TaskItem` não está exibindo o texto da tarefa, precisamos corrigir isso, incluindo uma `prop` no `TaskItem` para
receber a `Task` como parâmetro:
```html
<template>
  <div class="bg-white shadow p-4">{{ task.text }}</div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component'
import { Task } from '@/model/Task'

@Options({
  props: {
    task: Task
  }
})
export default class TaskItem extends Vue {
  task!: Task
}
</script>

```
No código acima fizemos o seguinte:
- Definimos a prop no `@Options`, com o nome e o Tipo que esperamos.
- Definimos a variável `task` na classe `TaskItem`, declarando o tipo novamente. Repare que colocamos um `!`, isso é
necessário por que o Typescript não pode garantir que aquela variável não será nula, porém nós temos certeza que ela não
será nula pois o Vue irá passa-la via prop. Usar `!` é muito arriscado, só nos damos o luxo de fazer isso nessas situações.
- Exibimos o `text` da `task` no `<template>`.
- Aproveitamos para darmos um estilo nesse elemento: fundo branco, sombra e padding de 16px.

E para finalizarmos a integração dos componentes só precisamos passar a tarefa através dessa `prop` que acabamos de criar,
então voltando para o `App.vue` podemos escrever o seguinte no `<template>`:
```html
<template>
  <div class="verti p-2 bg-gray-200 min-h-screen">
    <task-item v-for="todo in tasks" :key="todo.id" :task="todo" class="mb-2" />
  </div>
</template>
```
Estamos passando a variável `todo` para a `prop` chamada `task` dessa forma: `:task="todo"`

E aproveitamos novamente para dar uma estilizada na tela, usando o velho `verti`, alterando o padding, o fundo e em especial
usando a classe de CSS `min-h-screen` que faz com que a altura mínima desse elemento seja a altura da tela.

Também colocamos um `mb-2` aqui na `task-item` para dar um espaçamento entre eles. Gosto de definir margem, largura e
altura do lado de fora do componente para poder reutiliza-lo melhor.

# E é isso

Você aprendeu um básico de Typescript, renderizar listas e compor componentes, vai usar muito isso em seus apps.
Na próxima aula vamos implementar funções de adicionar e remover itens da lista com a interação do usuário.
