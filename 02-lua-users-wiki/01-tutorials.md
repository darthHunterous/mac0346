# 01 - Tutorials
* [Lua Users Wiki Tutorial](http://lua-users.org/wiki/TutorialDirectory)

### Ordem de Atribuição de variáveis
* Não definida
    * `a, a = 1, 2` não é possível definir com precisão qual valor `a` terá
    * Usar atribuições separadas quando a ordem for importante

### Usando o promp interativo de Lua como calculadora
* Prefixar cada expressão com `=`

### Pi
* `math.pi`

### Coerção Automática
* Evitar depender dela, garantindo que números em cálculos específicos sejam do tipo adequado

### Strings
* Aspas duplas, simples ou brackets duplos
* Sequências escapadas não são reconhecidas com brackets duplos
* Brackets duplos também servem para strings de várias linhas
* Brackets duplos permitem aninhamento porém os brackets mais externos precisam possuir um ou mais `=` entre eles
    * `[===[one [[two]] one]===]`

### Operadores
* `{} == {}`
    * Retorna `false` pois duas tabelas diferentes são criadas

### For
* Variável do for apenas visível dentro do laço, quando ele acaba não existe mais

### Tabelas
* `t.foo` equivalente a `t["foo"]`
    * `t[foo]` tentaria usar a variável `foo` como chave
* `t = {foo = "bar"}` equivalente a `t = {["foo"] = "bar"}`
* Comprimento de um array: `#t`
    * Não conta todos itens, encontra o último inteiro como chave
* `insert()` pode inserir no fim do array se não for passado o parâmetro do meio, senão insere em qualquer índice
* `remove()` remove um item do array e reduz o índice de todos os outros
* `ipairs()` itera sequencialmente pelo array
* `table.concat()` junta todos os elementos de um array de acordo com um dado separador

### Criar módulo
```lua
local mymodule = {}

function mymodule.foo()
    print("Hello world!")
end

return mymodule
```
* Importando o módulo
    ```lua
    local mymodule = require "mymodule"
    mymodule.foo()
    ```

### Criar uma tabela de módulo
* Uma maneira: Criar a tabela no topo e adicionar funções a ela
* Outra maneira: Fazer todas funções locais e colocá-las na tabela ao final
* Combinar as maneiras: adicionar na tabela após declarar cada função