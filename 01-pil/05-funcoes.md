# 05 - Funções
* Uma função é seguida por uma lista de argumentos entre parênteses, mesmo que não haja argumentos
    * Parênteses são opcionais apenas quando o argumento único é uma string literal ou um construtor de tabela
* Sintaxe especial para chamadas orientadas a objetos: `o:foo(x)` é equivalente a `o.foo(o, x)`
* As funções têm **nome**, lista de **parâmetros** e um **corpo**
    ```lua
    -- retorna a soma dos elementos de um array
    function add (a)
        local sum = 0
        for i,v in ipairs(a) do
            sum = sum + v
        end
        return sum
    end
    ```
* Parâmetros se comportam como variáveis locais inicializadas com os valores passados na chamada da função
* Uma função pode ser chamada com número de argumentos diferente do esperado: argumentos extras são descartados e parâmetros faltantes recebem `nil`
    * Exemplo de função sem parâmetro
    ```lua
    function incCount(n)
        n = n or 1
        count = count + n
    end
    ```
    * Lua atribui `nil` a `n` quando chamada só `incCount()`, que na primeira linha da função irá atribuir `1` a `n`

### 5.1 - Vários resultados
* Funções podem retornar vários resultados
* Exemplo em `string.find` retorna o índice onde determinado padrão começa e outro onde termina
    * `s, e = string.find("hello Lua users", "Lua")`
* Podem retornar vários resultados listando após o `return`
    * `return m, mi`
* Lua ajusta o número de resutlados de acordo com as circunstâncias: quando chamada como declaração, os resultados são descartados. Quando chamada como expressão, apenas o primeiro é mantido
* Uma chamada de função que não seja a última na lista produz apenas um resultado
    * `x, y = foo2(), 20`
        * `foo2()` produz `'a'` e `'b'` porém `x='a' e y=20`
* Um construtor coleta todos os resultados de uma chamada de função
    * `a = {foo2()}  -- a = {'a', 'b'}`
    * Só para a última chamada da lista, senão produz apenas um argumento
* Forçar retornar apenas um resultado por incluir um par adicional de parênteses
    * `print((foo2()))`
* `unpack()` recebe um array e retorna todos os elementos separadamente

### 5.2 - Número variável de Argumentos
* `...` como parâmetro indica a função com um número variável de argumentos
    * Acessível à função como a tabela `arg`, com o campo extra `n`, número de elementos obtidos
* Podemos usar as variáveis dummy `_` para escolher apenas o resultado da função que interessa ou `select()`
    * `select(1, string.find("hello hello", " hek"))  --> 6`
* Quando há argumentos fixos e outro número variável
    * `function g(a, b, ...) end`
        * `g(3) --> a=3, b=nil, arg={n=0}`
        * `g(3, 4) --> a=3, b=4, arg={n=0}`
        * `g(3, 4, 5, 8) --> a=3, b=4, arg={5, 8; n=2}`
* Para passar `arg` de uma função para outra chamada dentro desta, pode-se usar `unpack(arg)` na passagem de parầmetros`

### 5.3 - Argumentos Nomeados
* O mecanismo de passagem de parâmetros é posicional
* É **INVÁLIDO**: `rename(old="temp.lua", new="temp1.lua")`
    * O truque é passar os argumentos para uma tabela: `rename{old="temp.lua", new="temp1.lua"}` que será passada para outra função
        ```lua
        function rename(arg)
            return os.rename(arg.old, arg.new)
        end
        ```