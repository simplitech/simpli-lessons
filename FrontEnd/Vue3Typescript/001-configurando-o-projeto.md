# Configurando o projeto
Iremos criar um projeto com o framework front-end `Vue`, versão 3. Utilizando a linguagem de programação estáticamente-tipada `Typescript` e o pré-processador de estilos `SASS`.

Para isso iremos utilizar o programa de linha de comando `vue-cli`.

Então abra seu terminal e vamos começar ;)

## Instalando o Vue-cli
Para checar se já possui o vue-cli e em qual versão está, use o comando abaixo:
```
vue --version
```

Este guia foi adaptado para a versão **4.5.6**, porém versões posteriores também devem funcionar da mesma maneira.

### Para instalar o vue-cli use o comando abaixo:
```
npm i -g @vue/cli
```

Caso já possua o vue-cli e queira atualiza-lo use este comando:
```
npm update -g @vue/cli
```

## Utilizando o vue cli para criar o projeto
Execute o comando:
```
vue create nome-do-meu-projeto
```
<small>(Se não ficou claro, substitua "nome-do-meu-projeto" por qualquer nome que desejar)</small>

O Vue-cli irá perguntar como deseja configurar o projeto, escolha estas opções:

- Please pick a preset: `Manually select features`
- Check the features needed for your project: 
  - `Choose Vue version`
  - `Babel`
  - `TS`
  - `CSS Pre-processors`
  - `Linter`
- Choose a version of Vue.js that you want to start the project with `3.x (Preview)`
- Use class-style component syntax? `Yes`
- Use Babel alongside TypeScript (required for modern mode, auto-detected polyfills, transpiling JSX)? `Yes`
- Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): `Sass/SCSS (with dart-sass)`
- Pick a linter / formatter config: `ESLint + Standard config`
- Pick additional lint features: `Lint on save`
- Where do you prefer placing config for Babel, ESLint, etc.? `In dedicated config files`
- A última pergunta deve ser se quer salvar essa configuração, fique à vontade para escolher o que quiser

Aguarde a configuração ser finalizada.

## Executando o projeto
Agora é hora de verificar se deu tudo certo, execute o comando abaixo para rodar sua aplicação:
```
npm run serve
```

## Instalando o Tailwind
Para facilitar o processo de criar layout e estilo da aplicação iremos usar a biblioteca de CSS `Tailwind`. Também iremos usar um plugin para facilitar o layout chamado `tailwindcss-linelay`.

### Instale o tailwind como dev-dependency:
```
npm i tailwindcss -D
```

### Instale o plugin como dev-dependency:
```
npm i tailwindcss-linelay -D
```

### Crie o arquivo `postcss.config.js` na raiz do projeto com o seguinte conteúdo:
```js
const tailwindcss = require('tailwindcss');

module.exports = {
  plugins: [
    tailwindcss,
  ],
};
```

### Crie o arquivo `tailwind.config.js` na raiz do projeto com o seguinte conteúdo:
```js
const TailwindLinelay = require('tailwindcss-linelay')

module.exports = {
  important: true,

  purge: false,

  plugins: [
    // horiz, verti, weight
    TailwindLinelay(),
  ],

  theme: {
    extend: {
      weight: theme => ({
        '1': [1],
        '2': [2],
        '3': [3],
        '4': [4],
        '5': [5],
      }),
    },
  },

  variants: ['responsive', 'hover'],
}
```

### Crie o arquivo `app.scss` dentro de uma pasta `src/assets/scss` com o seguinte conteúdo:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### Edite o arquivo `src/App.vue`, colocando na primeira linha dentro da tag `<style lang="scss">` o seguinte:
```scss
@import './assets/scss/app.scss';
```

Certifique-se que essa tag style não está marcada como `scoped`.

## E é isso

Toda vez que você modificar arquivos que estão fora da pasta `src` terá que reiniciar a aplicação, portanto, para ver se funcionou execute o comando seguinte novamente:
```
npm run serve
```

Pronto, no próximo capítulo veremos como todas essas tecnologias podem te ajudar, até lá você pode modificar os arquivos `src/App.vue` e `src/components/HelloWorld.vue` para dar aquela experimentada básica.
