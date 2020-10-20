#Interações com a lista de tarefas
Na aula anterior começamos a criar a nossa lista de tarefas, mas ela só exibe tarefas pré-definidas no código.

Hoje vamos deixa-la realmente funcional.

## Crie um atributo para a nova tarefa
```typescript
  tempTask = new Task(0, '')
```
Precisamos criar esse atributo dentro da classe de Typescript `App` do nosso componente, pode coloca-la abaixo das
`tasks`. Iremos usar essa variável para armazenar o conteúdo da tarefa que ainda não foi salva na lista, por isso
colocamos o nome `tempTask`.

## Crie um método para salvar a nova tarefa
```typescript
  submitTempTask () {
    this.tempTask.id = Math.random()
    this.tasks.push(this.tempTask)
    this.tempTask = new Task(0, '')
  }
```
Este método será executado quando o usuário decidir salvar a tarefa
- Estamos colocando um número aleatório para o id da tarefa, para que todas as tarefas não fiquem com o mesmo id de zero
- Estamos "empurrando" essa tarefa para dentro do array de tarefas que havíamos criado na aula anterior
- Estamos definindo que agora o tempTask irá apontar para uma nova task. Isso é importante para que novas tasks não
fiquem alterando tasks que já colocamos no array

## Inclua um formulário no template
```html
    <form @submit.prevent="submitTempTask" class="horiz space-x-4 mb-10">
      <input type="text" v-model="tempTask.text" placeholder="Nova tarefa"
             class="weight-1 p-2 border border-gray-500 shadow-inner" />
      <button type="submit" class="bg-gray-900 text-white w-32">Enviar</button>
    </form>
```
Esse formulário irá definir a interação com a variável e o método que criamos:
- Criamos um `<form>` e estamos escutando o evento `@submit` para executarmos o método `submitTempTask` quando o usuário
enviar o conteúdo do formulário
    - Note que colocamos o modificador `prevent` no submit, isso é necessário para a página não se recarregar
    automaticamente
    - Também colocamos a classe de CSS `horiz` para deixar os itens um ao lado do outro, a classe de CSS `space-x-4`
    para dar um espaço entre os itens e a classe de CSS `mb-10` para colocar uma margem abaixo do formulário
- Dentro do formulário colocamos um `<input>` do tipo "texto", ele vai exibir e modificar o valor `tempTask.text`, isto
é, irá exibir e modificar o texto da tarefa temporária
    - Colocamos um placeholder para deixar claro para que serve aquele input
    - Colocamos a classe `weight-1`, essa classe de CSS faz com que o elemento ocupe todo o espaço que está sobrando.
    Caso existam 2 elementos irmãos com `weight-1` ambos vão dividir igualmente o espaço que está sobrando dentro do
    elemento pai. Caso exista um `weight-1` e um `weight-2` o segundo irá ocupar o dobro do espaço do primeiro, e por aí
    vai.
- Também colocamos um botão do tipo "submit", isso quer dizer que este botão irá executar o `@submit` do formulário ao
ser clicado.

Você me pergunta: Mas pra que criar um formulário com submit ao invés de simplesmente colocar um `@click` no botão?

E eu te respondo: Se o usuário estiver digitando e pressionar a tecla "Enter" do teclado também executará o submit. Mas
além de termos essa conveniência posso te dizer que essa é uma boa prática, formulários foram feitos para serem
controlados dessa maneira.

Se você testou a aplicação já deve ter percebido que já podemos incluir novas tarefas na nossa lista, e foi fácil, vai.

## Não precisamos mais de uma lista pré-definida

Vamos apagar aquela lista que criamos no código, para que o usuário começe sua lista sem nenhuma entrada. Basta
substituir aquele código da lista por isso:
```typescript
  tasks: Task[] = []
```
Veja que não basta deixar o array vazio, pois o compilador não conseguiria entender que é um array de Tasks se você não
colocou nenhuma Task dentro. Precisamos explicitamente dizer o tipo, no caso `Task[]`.

## Implementando a opção de remover uma Task

Nosso objetivo é colocar um icone de remover na linha de cada Task e ao clicar no icone eliminar aquela task.
Mas existe um pequeno desafio nessa tarefa: O icone de remover precisa estar dentro do componente `TaskItem.vue` enquanto
o array de tasks está fora desse componente, no `App.vue`. Então precisaremos trabalhar com eventos para realizar essa
comunicação de um componente de dentro pra fora, é simples, vamos lá.

#### Declare que o componente irá emitir o evento `remove`
O `@Options` do `TaskItem.vue` vai ficar assim:
```typescript
@Options({
  props: {
    task: Task
  },
  emits: ['remove']
})
```

#### Definindo o icone
Agora, no `<template>` do `TaskItem.vue` iremos colocar o icone de remover ao lado direito do texto Vai ficar assim:
```html
<template>
  <div class="horiz items-center bg-white shadow p-4">
    <span class="weight-1">{{ task.text }}</span>
    <a @click="$emit('remove')" class="task-item__delete w-6 h-6" />
  </div>
</template>
```
Explicando:
- Colocamos a classe `horiz` e `items-center` para definir o layout
- Ao invés de colocarmos o texto diretamente, envolvemos ele com uma tag `<span>` com classe `weight-1`
- E para o icone de remover, adicionamos a tag `<a>` dentro do layout
    - Na tag colocamos o evento de `@click`, quando o usuário clicar no icone iremos executar o código `$emit('remove')`,
    que por sua vez irá avisar ao componente pai (nesse caso o `App.vue`) que isso aconteceu.
    - Também colocamos a classe de CSS customizada `task-item__delete` para definirmos visualmente como será este icone
    
#### Definindo a classe de CSS customizada
Como este icone possui uma imagem, precisamos criar uma classe de CSS que defina qual será a imagem usada. Crie a tag `<style>` com o seguinte conteúdo:
```html
<style lang="scss" scoped>
.task-item__delete {
  @apply cursor-pointer bg-contain;
  background-image: url("../assets/img/delete.svg");
}
</style>
``` 
Explicando:
- Podemos usar o `@apply` para usar herdar classes do tailwind nessa nossa classe customizada, nesse caso estamos
herdando:
    - `cursor-pointer` para fazer com que o cursor do usuário vire uma mãozinha com o dedinho apontando ao passar por
    cima do elemento. Isso nem sempre é necessário, se você colocar o atributo `href` no `<a>` esse comportamento é
    automático. Eu gosto de definir em um CSS global para fazer com que todas as tags `<a>` tenham `cursor-pointer`, mas
    vamos deixar as coisas simples por enquanto.
    - `bg-contain` faz com que a imagem do elemento fique contida dentro do mesmo. Por isso colocamos w-6 e h-6 no
    elemento. Prefiro sempre definir questões de layout no próprio elemento ao invés da classe customizada, fica mais
    explicito.
- Agora vem a parte onde definimos a imagem, o código: `background-image: url("../assets/img/delete.svg");` faz isso.
    - Estamos indicando que a imagem de fundo está na pasta `assets/img` com o nome `delete.svg`. Mas pra isso funcionar
    precisamos colocar essa imagem nessa pasta. Recomendo você acessar o site [flaticon.com](http://flaticon.com), fazer
    download de algum icone no formato SVG, renomea-lo para `delete.svg` e colocar na pasta correta.

#### Escutando o evento personalizado
Certo, agora basta escutarmos nosso evento personalizado e executar o código que remove a Task. Em `App.vue` altere o
trecho do `<template>` que declaramos as tasks para o seguinte:
```html
    <task-item v-for="todo in tasks" :key="todo.id" :task="todo"
               @remove="removeTask(todo)" class="mb-2" />
```
Aqui, com o código `@remove="removeTask(todo)"` estamos simplesmente definindo que iremos executar o método `removeTask`
passando a task como parâmetro quando o `TaskItem.vue` nos avisar com o evento `remove`.

#### Método de remover
```typescript
  removeTask (task: Task) {
    const indexToRemove = this.tasks.findIndex(t => t.id === task.id)
    this.tasks.splice(indexToRemove, 1)
  }
```
Explicando:
- O código `this.tasks.findIndex(t => t.id === task.id)` está procurando dentro do array de `tasks` o indice da task que
possui o mesmo `id` da task que queremos remover. Precisamos disso pois a melhor maneira de remover um item do array é
usando seu indice. Guardamos este valor na variável `indexToRemove`. Para definir variáveis dentro do escopo do método
podemos usar a palavra `const`. Mas caso precise alterar o valor da variável depois de definida altere `const` para `let`.
- Com o código `this.tasks.splice(indexToRemove, 1)` removemos `1` elemento apartir do indice indicado.

## Persistindo os dados no navegador
Com a aplicação que temos hoje, se o usuário recarregar a página ou sair dela e voltar outro dia não encontrará mais
suas tarefas. Com pouco esforço podemos corrigir isso salvando as tarefas na memória do navegador.

#### Crie um método para salvar as tasks no localStorage
```typescript
  saveTasks () {
    localStorage.setItem('tasks', JSON.stringify(this.tasks))
  }
```
Com o código `JSON.stringify(this.tasks)` estamos convertendo as `tasks` em uma string no formato JSON e após isso
estamos salvando este dado com o nome `tasks` no `localStorage`, que é uma ferramenta para guardar strings na memória do
navegador

#### Crie um método para ler as tasks do localStorage
```typescript 

  loadTasks () {
    const fromStorage = localStorage.getItem('tasks')
    if (fromStorage) {
      this.tasks = JSON.parse(fromStorage)
    }
  }
```
Estamos obtendo as tasks em formato JSON e colocando-as em uma variável nullable. Verificamos se a variável não é nula,
se não for, convertemos a string para objeto e colocamos na variável `tasks` para ser usada pela aplicação

#### Usando o método de salvar
Agora basta chamarmos o método `saveTasks` sempre que modificarmos as tasks, alterando os métodos de salvar e remover
task para chamar no final:
```typescript
  submitTempTask () {
    this.tempTask.id = Math.random()
    this.tasks.push(this.tempTask)
    this.tempTask = new Task(0, '')
    this.saveTasks()
  }

  removeTask (task: Task) {
    const indexToRemove = this.tasks.findIndex(t => t.id === task.id)
    this.tasks.splice(indexToRemove, 1)
    this.saveTasks()
  }
```

#### Usando o método de ler
Vamos usar o método `mounted`, que é executado quando o componente é carregado, para ler as tasks do localStorage:
```typescript
  mounted () {
    this.loadTasks()
  }
```

## E é isso

Com essa aula você aprendeu diversos recursos práticos para sua aplicação.

Na próxima aula iremos incrementar ainda mais nossa aplicação.
