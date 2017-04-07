# Aprenda Istanbul

[![Build Status](https://travis-ci.org/dwyl/learn-istanbul.svg?branch=master)](https://travis-ci.org/dwyl/learn-istanbul)
[![codecov](https://codecov.io/gh/dwyl/learn-istanbul/branch/master/graph/badge.svg)](https://codecov.io/gh/dwyl/learn-istanbul)
[![devDependency Status](https://david-dm.org/dwyl/learn-istanbul/dev-status.svg)](https://david-dm.org/dwyl/learn-istanbul#info=devDependencies)


> Aprenda como usar o Istanbul para checar/monitorar o _Code Coverage_ em seus projetos Javascript.

❗ Notas de tradução
=================

- Alguns trechos de código foram traduzidos para que o entendimento seja melhor de acordo com o contexto inserido
- Algumas palavras são melhor definidas em sua língua original, portanto tais palavras **não** foram traduzidas. As mesmas terão um link de referência após para melhor entendimento
- O texto possui algumas alterações de sintaxe e estrutura para facilitar a tradução.
- O texto também pode possuir algumas alterações de estrutura para se adaptar à lingua traduzida

- - -

![Sign Not In Use](https://cloud.githubusercontent.com/assets/194400/14234939/5e182636-f9e7-11e5-9fa3-7509389416be.jpeg "Sign not in use!")

## Por que?

Como a placa que "***Não está em uso***" muito código escrito ***nunca*** chega a ser ***executado***.

Existem *alguns* problemas óbvios com isso:
1. Se um código não testado fica na base ele pode conter um ***comportamento desconhecido***, por exemplo, ***bugs***.
2. Recursos não testados são mais ***difíceis de manter*** sem introduzir as chamadas ***breaking changes*** (mudanças que quebram a compatibilidade com a versão anterior).
3. Código não testado pode ***desordenar*** um projeto e acumular [***débito técnico***](https://en.wikipedia.org/wiki/Technical_debt) o que é uma ***perda de tempo***

## O que?

**Code coverage** (Cobertura de código em livre tradução) te diz quais códigos que você escreveu estão, de fato, sendo executados. Desta forma você pode decidir se as linhas que não estão cobertas pelo *coverage* são [supérfluas](https://www.google.com.br/search?q=superflo&gws_rd=cr&ei=rqznWLy5E8mxwASDjqWwDg#safe=off&q=sup%C3%A9rfluo)
(*e podem ser removidas*) *ou* requerem testes adicionais.

O resto desta página vai focar na parte *prática*, então, se você é completamente novo em Code Coverage, nós recomendamos que você leia [este artigo da wikipédia](http://en.wikipedia.org/wiki/Code_coverage) antes

*Istanbul* É um script de análise de code coverage que você pode rodar quando executa seus testes unitários:
https://github.com/gotwarlost/istanbul/

Gostamos dele porque é simples e printa relatórios HTML bastante bonitos (*veja abaixo*).

## Como?

### Instalação

Nós preferimos instalar o istanbul como uma "*devDependency*" em cada um de nossos projetos:

```sh
npm install istanbul --save-dev
```

Para checar se tudo correu bem, copie e rode o código a seguir no terminal:

```sh
node_modules/.bin/istanbul help
```


### Exemplo simples

Para nosso primeiro exemplo, vamos criar um arquivo chamado `test.js`.

```sh
vi test.js
```

digite (ou copie) o código a seguir dentro do arquivo criado:

```javascript
x = 42;
if(false)
     x =-1;
```

Agora execute o comando do istanbul para gerar um relatório de execução:

```sh
node ./node_modules/.bin/istanbul cover test.js
```

Alternativamente você também pode inserir a linha:

```json
"coverage": "istanbul cover ./test.js"
```

no seu `package.json`, na sessão de scripts, e executar:

```sh
npm run coverage
```

Isto vai criar um diretório na pasta de seu projeto chamado __coverage__, aonde você poderá encontrar todos os relatórios gerados pelo istanbul.

No nosso caso:
[learn-istanbul/**coverage/lcov-report**/learning-istanbul/**test1.js.html**]()

Se você abrir o arquivo `test1.js.html` no seu navegador, então será possível ver uma representação ___visual___ do seu relatório de cobertura:

![Basic coverage report](https://cloud.githubusercontent.com/assets/194400/14235269/27f13d9a-f9f1-11e5-9b43-5c8c659717e0.png)

O Istanbul nos dá quatro métricas de cobertura:

- __Statements__: Quantos dos [statements](http://www.2ality.com/2012/09/expressions-vs-statements.html) do seu código são executados.
- __Branches__: Expressões condicionais (como `if`, `case`) criam _branches_ (ramificações) de código que podem não ser executadas (exemplo: `if/else`). Esta métrica te diz quantas dessas ramificações foram executadas.
- __Funções__: A proporção de funções que foram definidas por você que foram de fato chamadas
- __Linhas__: A quantidade de linhas, em proporção, que foram executadas.

quando você clica no link `test.js` dentro do relatório para ver a _coverage_ do arquivo você receberá a seguinte tela:

![learn-istanbul-test js_html](https://cloud.githubusercontent.com/assets/194400/14235369/2e87e3cc-f9f4-11e5-8701-09a5c538f98e.png)

Duas coisas que precisamos destacar no exemplo acima:

- Nós só obtemos 66.67% da _coverage_ porque só 2/3 do código foi executado.
- A terceira linha nunca vai ser executada porque `false` é _sempre_ ___false___!

Este pode ser um exemplo meio _trivial_ mas ele mostra exatamente aonde o código inútil está.

#### Um problema mais "real"

Tente executar o arquivo `mischief.js` rodando `npm test`:

![learn-istanbul-terminal-run](https://cloud.githubusercontent.com/assets/194400/14235496/d5ae4f4e-f9f7-11e5-9388-c50dcca10cbf.png)

O quie está errado com a foto a seguir?

![96 % Code Coverage](https://cloud.githubusercontent.com/assets/194400/14235348/7d80c1de-f9f3-11e5-88bd-de9e4d792c3b.png)

Existem _muitos_ desenvolvedores/empresas que só sonham em ter 96% de _code coverage_! E ainda sim, quando espiamos os detalhes do teste, percebemos que deixamos uma coisa ___grande___ passar.

![learn-istanbul-mischief-on-line-34](https://cloud.githubusercontent.com/assets/194400/14235401/3d8c14fa-f9f5-11e5-946a-d57484b46ce7.png)

Nós temos uma _code coverage_ 100% funcional, mas temos somente 50% da "__branch__ coverage", ou seja, isto significa que uma ou mais _ramificações condicionais_ não estão sendo executadas.

Na maioria do tempo isso vai ser uma coisa qualquer, mas o que acontece se alguém desavisado deixar uma coisa assim passar:

```javascript
if(employee.status === 'terminated' && employee.left - today() > 90) {
	selfDestuct();
}
```

De repente __97% de coverage__ não parece mais tão legal não é?

E se adicionassemos um teste que _segue_ a ramificação que contem esse _rogue code_ (código não executado)? Vamos conseguir chegar na nossa mítica _coverage_ de 100%:

![learn-istanbul-mischief-100-percent](https://cloud.githubusercontent.com/assets/194400/14235536/2e274184-f9f9-11e5-8999-9dc92cbb3486.png)

Mas se simplesmente deixarmos que este código seja promovido sem nenhuma checagem posterior, o _rogue code_ vai estar em produção e logo mais será esquecido.

![100 % Code Coverage includes Rogue Code](https://cloud.githubusercontent.com/assets/194400/14235588/c18e7ee6-f9fa-11e5-93c8-48ba6a25f2da.png)

A solução aqui é _não_ confiar (_somente_) em ferramentas como o Istanbul para checar o código. Isto faz com que haja uma separação entre as pessoas escrevendo os testes e os desenvolvedores que escrevem o código.

E ainda sim, ___não existe nenhum substituto para uma boa___ __Code Review__!


![87% Test Coverage](http://i.imgur.com/NTI4Pxw.png)

<br />

## Coverage como um serviço

Saber a _coverage_ localmente é legal, mas é _muito_ mais fácil e útil para um time trackear seus testes ao longo do tempo usando um serviço independente.

Ainda bem que temos como escolher:

+ ***Codecov***: https://codecov.io/#features
+ Coveralls: https://coveralls.io/features
+ Codeclimate: https://codeclimate.com/features
+ Codacy: https://codacy.com/product

Nós usamos todos os serviços e descobrimos que o ___Codecov___ é o _vencedor_ por 3 razões:
1. Fácil de integrar com sistemas de integração continua como o [Travis-CI](https://github.com/dwyl/learn-travis)
2. Ótimas métricas e visualizações
3. A mensagem da _Pull Request_ mostra se a coverage vai mudar com a nova adição do código (Veja abaixo)

### Setup

1. Faça sua conta (grátis) no Codecov usando sua conta do GitHub: https://codecov.io/
2. Selecione o repositório que você irá trackear a _coverage_ a partir da lista
3. Adicione algumas linhas no seu provedor de integração contínua, por exemplo no Travis:

```yml
before_install:
  - pip install --user codecov
after_success:
  - codecov --file coverage/lcov.info --disable search
```

Este script vai instalar o Codecode Reporter Toll no seu CI e enviar o arquivo `lcov.info` (_que é gerado pelo Istanbul_) para o Codecov, aonde ele será mantido em segurança.

> Exemplo real em um arquivo `.travis.yml`: https://github.com/dwyl/hapi-auth-jwt2/blob/master/.travis.yml#L7-L10

Agora quando você criar uma _pull request_, seu provedor de CI vai enviar esse relatório de _code coverage_ para o Codecov, e então o Codecov vai deixar uma mensagem como _comentário_ na sua PR:

![learn-istanbul-large-project-with-100-test-coverage](https://cloud.githubusercontent.com/assets/194400/17811233/964272d4-6619-11e6-8a42-32752453169b.png)

> Sim, é "_possível_" ter um repositório "[grande](https://github.com/FAC-GM/app/pull/553)" com 100% de _coverage_ (Todos os projetos da DWYL tem)

Então você consegue ver ao bater o olho se o novo código está sendo adicionado sem corresponder aos testes:

![learn-istanbul-codecov-lower](https://cloud.githubusercontent.com/assets/194400/17810879/96453d04-6617-11e6-8d3d-bfa3175d9973.png)

Quando a _Coverage_ diminui, a PR falha:

![learn-istanbul-pr-fails-on-lower-coverage](https://cloud.githubusercontent.com/assets/194400/17811085/d0a93364-6618-11e6-9694-12a033f2be63.png)

> __Nota__: se você quer _evitar_ que pessoas (_seu time_) crie _pull requests_ com menos do que o nível acordado de _coverage_ (100%, por exemplo) adicione um script de checagem de [`coverage`](https://github.com/dwyl/hapi-auth-jwt2/blob/452368830d0d7971f93219245f6cad56f2ece9e6/package.json#L57) e um [`pre-commit hook`](https://github.com/dwyl/hapi-auth-jwt2/blob/452368830d0d7971f93219245f6cad56f2ece9e6/package.json#L64) correspondente. Mais _pre-commit hooks_: https://github.com/dwyl/learn-pre-commit


#### Adicione uma badge no seu readme (_opcional/recomendado_)

Vá para `Settings` no seu repositório do Codecov, clique em `Badge` e selecione `Copy`:

![codecov-settings-badge](https://cloud.githubusercontent.com/assets/194400/17809827/122398f0-6611-11e6-8ac5-c2f828ec43c8.png)

Então cole o markdown no seu readme.

> Descubra mais sobre _badges_: https://github.com/dwyl/repo-badges

### Leitura complementar:

+ Criando um dashboard de code coverage com Javascript e Codecov, por @ariya
https://ariya.io/2015/08/javascript-code-coverage-dashboard-with-codecov-io

### Leitura ainda mais complementar

- Tutorial basico do Ariay: http://ariya.ofilabs.com/2012/12/javascript-code-coverage-with-istanbul.html
- _Coverage_ usando Jasmine: http://architects.dzone.com/articles/code-coverage-jasmine-tests

Istanbul (A ferramenta de _Code Coverage_ para Javascript)
https://github.com/gotwarlost/istanbul
__não__ deve ser confundida com [istanbul](https://wiki.gnome.org/Istanbul)
o gravador de desktop, eles são animais completamente diferentes!
Uma pena a colisão de nomes... :-(

[![HitCount](https://hitt.herokuapp.com/dwyl/learn-istanbul.svg)](https://github.com/dwyl/learn-istanbul)
[![Join the chat at https://gitter.im/dwyl/chat](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/dwyl/chat?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

## Licença

MIT © [Dwyl](http://www.dwyl.io), tradução [Lucas Santos](http://lsantos.me)

- Fale com o tradutor diretamente em [lhs.santoss@gmail.com](mailto:lhs.santoss@gmail.com) ou no twitter [@_staticvoid](http://twitter.com/_staticvoid)

> Tradução feita de forma independente, sem nenhuma conexão com o autor.
