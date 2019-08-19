# 06 - Mais sobre funções
* Funções são valores de primeira classe, ou seja, podem ser armazenadas em variáveis, passadas como argumentos e retornadas por funções
* Lexical scoping: funções podem acessar variáveis de funções que as circundam (Lua contém propriedades do cálculo Lambda)
* Funções são anônimas, seus identificadores na verdade se referem a uma variável que guarda a função
* `function foo (x) return 2*x end` é um **açúcar sintático** para `foo = function (x) return 2*x end`
* Função para ordenar uma tabela de acordo com o campo `name` em ordem alfabética reversa
    ```lua
    table.sort(network, function(a,b)
        return (a.name > b.name)
    end)
    ```
* **Higher-order function**: recebe outra função como argumento
    * Consequência de Lua lidar com funções como valores de primeira classe

### 6.1 - Closures
* Função escrita dentro de outra tem acesso a todas variáveis locadas da função que a circunda (lexical scoping)
    * Exemplo
        ```lua
        function sortbygrade (names, grades)
            table.sort(names, function (n1, n2)
                return grades[n1] > grades[n2]
            end)
        end
        ```
        * `grades` é local à função que circunda a função anônima. Dentro da função anônima é considerada uma **variável local externa** ou **upvalue**
* **Closure**: uma função junto com tudo que necessita para acessar seus upvalues corretamente
    ```lua
    function newCounter()
        local i = 0
        return function ()
                   i = i+1
                   return i
                end
    end

    c1 = newCounter()
    print(c1()) --> 1
    print(c1()) --> 2

    c2 = newCounter()
    print(c2()) --> 1
    print(c1()) --> 3
    print(c2()) --> 2
    ```
    * `c1` e `c2` são **closures** diferentes e agem sobre instâncias diferentes da variável local `i`
* Closures são úteis para callbacks também
* Exemplo de closure para redefinir uma função como `sin` de radianos para graus
    ```lua
    do
        local oldSin = math.sin
        local k = math.pi/180
        math.sin = function (x)
            return oldSin(x*k)
        end
    end
    ```
* **Sandboxes**: criam ambientes seguros para executar código não verificado
    * Um exemplo seria restringir o acesso da função `open` sem ser através de uma nova definição que checa se é ok executá-la

### 6.2 - Funções Não Globais
* Armazenar uma função em uma variável global gera uma **função local**, função restrita a um dado escopo
    ```lua
    local f = function (...)
        ...
    end

    -- com açúcar sintático
    local function f (...)
        ...
    end
    ```
    * Problemas com funções recursivas pelo fato da variável local ainda não ter sido definida, pode ser resolvido com uma declaração prévia antes da atribuição, do tipo `local fact`
        * Não funciona para funções recursivas que chamam outra indiretamente antes de sua definição. Resolver com:
            ```lua
            local f, g -- forward declarations

            function g()
                ... f() ...
            end

            function f()
                ... g() ...
            end
            ```

### 6.3 - Tail Calls
* **Tail Call**: quando uma função chama outra como última ação
    ```lua
    function f(x)
        return g(x)
    end
    ```
* **Proper tail call**: quando uma linguagem não precisa armazenar informações de uma função que chama outra ao fazer uma tail call (Lua é uma delas)
    * Por não usar espaço na pilha, não há limites de tail call em sequência
* Apenas `return g(...)` é tail call, coisas como `return g(x) + 1`, `return x or g(x)` e `return (g(x))` requerem adições ou ajustes para um dado resultado de return
    * Porém `g` e seus argumentos podem ser expressões, pois Lua avalia eles antes da chamada
* Um exemplo de uso de tail call é num programa de máquinas de estado, em que pode-se representar um labirinto em que cada movimento retorna uma função correspondente à sala em que se deseja movimentar para