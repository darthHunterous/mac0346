# 04 - Declarações
* Declarações convencionais: atribuições, estruturas de controle e chamadas de procedimentos
    * Suporte a atribuições múltiplas e declarações locais de variáveis

### 4.1 - Atribuição
* Mudar valor de variável ou campo de tabela
    * `t.n = t.n + 1`
* **Atribuição múltipla**: `a, b = 10, 2*x`
    * Lua primeiro avalia os valores e depois executa atribuições, pode ser usada para trocar valores: `x, y = y, x`
* Lua ajusta os valores ao número de variáveis, descartando ou atribuindo `nil`
    * `a, b, c = 0, 1  --> 0 1 nil`
    * `a, b = a+1, b+1, b+2  --> valor de b+2 ignorado`
* Mais útil para trocar valores ou obter vários valores de retorno de uma função
    * `a, b = f()`
        * Onde `f()` retorna dois valores

### 4.2 - Variáveis locais e blocos
* `local i = 1`
* Escopo limitado ao bloco em que foram declaradas
* **Bloco**: corpo de estrutura de controle, função ou um chunk (arquivo ou string onde a variável foi declarada)
    ```lua
    x = 10
    local i = 1  -- local a todo o chunk

    while i<=x do
        local x = i*2  -- local ao while
        print(x)  --> 2, 4, 6, 8, ...
        i = i + 1
    end

    if i > 20 then
        local x  -- local ao then
        x = 20
        print(x+2)
    else
        print(x)  --> x global, 10
    end

    print(x)  --> x global, 10
    ```
* É uma boa prática usar variáveis locais sempre que possível
    * Evita lotar o ambiente global com nomes desnecessários, e locais tem acesso mais rápido que globais
* Prática comum é criar uma variável local para armazenar o valor de uma global: `local foo = foo`
    * Preserva o valor original de `foo` e acelera o acesso
* Explicitar bloco com `do` e `end`
    ```lua
    do
        local a2 = 2*a
        local d = sqrt(b^2 - 4*a*c)
        x1 = (-b + d)/a2
        x2 = (-b - d)/a2
    end  -- escopo de a2 e d se encerra aqui
    print(x1, x2)
    ```

### 4.3 - Estruturas de Controle
* `if` para condicional e `while`, `repeat`, `for` para iteração
*  `end` encerra o `if`, `while`, `for` e `until` encerra o `repeat`
* **4.3.1 - If/else**
    * Parte do `else` opcional
    * `elseif` para `if` aninhados, evita múltiplos `end`
        ```lua
        if op == "+" then
            r = a + b
        elseif op == "-" then
            r = a - b
        elseif op == "*" then
            r = a * b
        elseif op == "/" then
            r = a / b
        else
            error("invalid operation")
        end
        ```
* **4.3.2 - While**
    * Primeiro testa a condição, se falso, encerra o laço
* **4.3.3 - Repeat**
    * Corpo sempre executado ao menos uma vez pois repete a condição até ela se tornar verdadeira
* **4.3.4 - For numérico**
    ```lua
    for var=exp1,exp2,exp3 do
        something
    end
    ```
    * Executa `something` de `exp1` até `exp2` vezes, acrescentando por passos de `exp3`
    * `exp3` é opcional, quando não presente, supõe-se passos de um
    * Todas 3 expressões são avaliadas uma vez, antes do laço começar, mesmo caso se houver uma função `f(x)` como expressão
    * A variável de controle é visível apenas dentro do laço. Para manter seu valor após um `break` deve-se salvar em outra variável
    * Não deve-se modificar o valor da variável de controle pois os efeitos são imprevisíveis. Deve-se usar `break`
* **4.3.5 - For genérico**
    * `for i,v in ipairs(a) do print(v) end` para Array
    * `for k in ipairs(t) do print(k) end` para Tabela
    * As variáveis do laço são locais e nunca devem ser sobreescritas
    * Exemplo dos dias da semana:
        * Para se encontrar o número de um dado nome para dia na semana, pode-se criar uma tabela reversa
            ```lua
            revDays = {}
            for i,v in ipairs(days) do
                revDays[v] = i
            end
            print(revDays["Tuesday"])
            ```

### 4.4 - Break e Return
* Permitem sair de um bloco interior
* `break` encerra um laço
* `return` retorna valores de uma função ou apenas a encerra
    * Há um return implícito no final de uma função
* `break` ou `return` só podem aparecer como a última declaração de um bloco (por questões sintáticas)
    * Para debuggar no meio de um bloco, pode-se usar `do return end` pois só `return` causaria erro