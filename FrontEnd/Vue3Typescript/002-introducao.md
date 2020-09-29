# Apagando o conteúdo que foi gerado 
Para começarmos a experimentar o Vue, vamos ajustar algumas coisas que foram geradas na última aula que servem apenas
para exemplo

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

## Remova arquivos de exemplo
Pode apagar estes arquivos:
- src/components/HelloWorld.vue
- src/assets/logo.png

# Exibindo dados Declarativamente

O Vue nos deixa exibir dados da aplicação através de uma sintaxe bem direta.
Veja que no nosso `src/App.vue` existe um trecho de código para o `<template>`,
nele podemos declarar algo como: 
```html
<template>
  <div>
    Contador: {{ counter }}
  </div>
</template>
```
No código acima estamos usando uma variável chamada `counter`, mas a variável 
ainda não foi declarada, então usamos um outro trecho de código para nosso `<script>`.
É nele que declaramos variáveis, métodos, e a maioria da lógica de programação:
```html
<script lang="ts">
import { Options, Vue } from 'vue-class-component'

@Options({})
export default class App extends Vue {
  counter = 0
}
</script>
```
No código acima estamos:
- importando alguns tipos usados por nosso componente
- declarando nosso Options, iremos conversar sobre ele mais pra frente
- declarando e exportando a classe do nosso componente, chamada App, que herda de Vue
- declarando uma variável da instância dessa classe, chamada counter e inicializada com 0

Nosso App já está funcionando, apesar de não parecer muito interessante já podemos notar
que estamos exibindo `Contador: 0` na página, isso acontece pois o Vue renderizou a variável.
E em breve vamos notar a magia da "Reatividade": ao modificar a variável `counter` o valor exibido na
página irá se atualizar automaticamente. Modifique o script dessa forma:
```html
<script lang="ts">
import { Options, Vue } from 'vue-class-component'

@Options({})
export default class App extends Vue {
  counter = 0

  mounted () {
    setInterval(() => {
      this.counter++
    }, 1000)
  }
}
</script>
```
No código acima criamos um método chamado `mounted` que vai ser chamado automaticamente na inicialização
do componente, é necessário que o método possua exatamente este nome, falaremos mais sobre o assunto no futuro.
Se você está acostumado com o `setInterval` já deve saber que ele executa um callback de tempos em tempos,
neste caso, incrementa o valor de `counter` a cada 1000 milisegundos (1 segundo).

Além de "interpolação de texto", podemos alterar atributos das tags HTML da seguinte maneira:
```html
<template>
  <div :title="loadTime">
    Contador: {{ counter }}
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component'

@Options({})
export default class App extends Vue {
  counter = 0
  loadTime = 'Você carregou a página em ' + new Date().toLocaleString()

  mounted () {
    setInterval(() => {
      this.counter++
    }, 1000)
  }
}
</script>
```
No código acima introduzimos um novo conceito, usamos um `bind` para tornar uma propriedade da tag HTML reativa. Ao
declarar `:title="loadTime"` estamos dizendo que o `title` da tag irá se atualizar automaticamente sempre que a variável
`loadTime` tiver o seu valor alterado. Rode o exemplo e passe o mouse acima do texto para entender melhor.

## Lidando com interação do usuário

Para permitir que o usuário interaja com o sistema precisamos escutar os eventos nativos nas tags HTML, por exemplo, um
clique num botão:
```html
<template>
  <div :title="loadTime">
    Contador: {{ counter }}
    <button @click="reverseMessage">Reverter mensagem</button>
    <span>{{ message }}</span>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component'

@Options({})
export default class App extends Vue {
  counter = 0
  loadTime = 'Você carregou a página em ' + new Date().toLocaleString()
  message = 'Olha eu aqui!'

  mounted () {
    setInterval(() => {
      this.counter++
    }, 1000)
  }

  reverseMessage () {
    this.message = this.message
      .split('')
      .reverse()
      .join('')
  }
}
</script>

<style lang="scss">
@import './assets/scss/app.scss';
</style>

```
Dessa vez criamos mais uma variável chamada `message`, e declarando o código `@click="reverseMessage"` na tag `button` 
iremos executar o método `reverseMessage` sempre que o usuário clicar no botão. E neste exemplo nosso método
simplesmente inverte a ordem das letras.

## Pausa na programação para um pouco de Estilo 😎
Já temos diversos elementos na página, mas agora eles estão todos bagunçados, tornando difícil a leitura, vamos usar
o tailwind para corrigir isso de uma maneira muito prática, alterando apenas o `<template>`:
```html
<template>
  <div :title="loadTime" class="verti items-center">
    Contador: {{ counter }}
    <button @click="reverseMessage" class="bg-black text-white p-4 m-2">Reverter mensagem</button>
    <span>{{ message }}</span>
  </div>
</template>
```
No `div` colocamos o código `class="verti items-center"`, são 2 classes:
- o `verti` (tailwindcss-linelay) faz com que
os elementos dentro do div sejam exibidos um abaixo do outro
- o `items-center` faz com que os itens fiquem centralizados, neste caso, horizontalmente pois usamos com um `verti`

No `button` colocamos o código `class="bg-black text-white p-4 m-2"`:
- `bg-black` deixa o fundo preto
- `text-white` deixa o texto branco
- `p-4` coloca um padding de 1rem em todos os lados, que na nossa configuração, é a mesma coisa que 16px
- `m-2` coloca uma margin de 0.5rem em todos os lados, 8px, lembre-se basta multiplicar a medida do tailwind por 4 para
saber o valor em pixels

## Binding de Mão-dupla, (two-way-binding)

Agora vamos dizer que você quer ler o texto que um usuário digita, podemos implementar essa funcionalidade usando o
`v-model`, neste exemplo só precisamos alterar o `<template>` pois já temos a variável `message` declarada:
```html
<template>
  <div :title="loadTime" class="verti items-center">
    Contador: {{ counter }}
    <div class="horiz items-center m-4">
      <input v-model="message" class="border border-black" />
      <button @click="reverseMessage" class="bg-black text-white p-4 m-2">Reverter mensagem</button>
    </div>
    <span>{{ message }}</span>
  </div>
</template>
```
Veja que criamos um `div` com classe `horiz` para que fiquem em alinhamento horizontal, e dentro dele colocamos um novo
`input` e o nosso velho `button`.

No `input` colocamos o código `v-model="message"`, isso faz com que ele receba o valor da variável `message` e que
também altere o valor da variável `message` conforme o usuário digitar.

## Exibindo Condicionalmente

E para encerrar essa aula iremos falar sobre o `v-if`, que permite ocultarmos algum elemento se uma condição é
verdadeira:

```html
<template>
  <div :title="loadTime" class="verti items-center">
    Contador: {{ counter }}
    <div class="horiz items-center m-4">
      <input v-model="message" class="border border-black" />
      <button @click="reverseMessage" class="bg-black text-white p-4 m-2">Reverter mensagem</button>
    </div>
    <span @click="clickedOnMessage = true">{{ message }}</span>
    <span v-if="clickedOnMessage">Olha o EasterEgg</span>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component'

@Options({})
export default class App extends Vue {
  counter = 0
  loadTime = 'Você carregou a página em ' + new Date().toLocaleString()
  message = 'Olha eu aqui!'
  clickedOnMessage = false

  mounted () {
    setInterval(() => {
      this.counter++
    }, 1000)
  }

  reverseMessage () {
    this.message = this.message
      .split('')
      .reverse()
      .join('')
  }
}
</script>
```
Criamos uma nova variável chamada `clickedOnMessage` que é inicializada como `false`. Colocamos um novo elemento que só
será exibido quando essa variável for verdadeira `<span v-if="clickedOnMessage">Olha o EasterEgg</span>`.

E para torna-la verdadeira colocamos um evento de clique em um elemento que já tinhamos antes
`<span @click="clickedOnMessage = true">{{ message }}</span>`.

## E é isso

Você já aprendeu diversos mecanismos do Vue e agora pode dar uma experimentada neles. Na próxima aula iremos criar uma
aplicação de verdade, uma lista de tarefas onde você pode adicionar, remover e marcar itens como concluídos. 
