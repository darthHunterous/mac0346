# 02 - Tipos e Valores
* Tipagem dinâmica
* **Oito tipos em Lua**
    ```lua
    --[[
    nil
    boolean
    number
    string
    userdata
    function
    thread
    table
    --]]
    ```
* **Função `type()`**: retorna uma string com o tipo de um dado valor
* Funções podem ser reatribuidas: `a = print`, depois `a(42)` imprime `42`

### 2.1 - Nil
* `nil`: principal propriedade de ser diferente de qualquer outro valor
* Variáveis globais têm `nil` como valor padrão
    * Podem ser deletadas recebendo `nil`
* Lua usa `nil` como não-valor, representa ausência de um valor útil

### 2.2 - Booleanas
* `false` e `true`
* Não detém exclusividade de valores condicionais: condicionais consideram `false` e `nil` como `false` e qualquer outro valor representa `true`
    * Zero e strings vazias são avaliadas como `true`

### 2.3 - Numbers
* `number` representa números reais de ponto flutuante com precisão dupla
    * Não há necessidade de representar número inteiros separadamente pois não há erros de arredondamento com doubles para número menores que `100,000,000,000,000` e CPUs modernas não são demoradas para aritmética de ponto flutuante
* Representação em notação científica: `4.57e+20`

### 2.4 - Strings
* Sequência de caracteres
* Não pode-se alterar um caracter dentro de uma string sem criar uma nova
    ```lua
    a = "one string"
    b = string.gsub(a, "one", "another")
    print(b) --> another string
    ```
* O gerenciamento de memória para strings é automático em Lua, que também consegue lidar com strings muito grandes de maneira bem eficiente
* Strings podem ser limitadas por `"` ou `'`
* **Sequências escapadas em Strings**
    ```lua
    --[[
        \a bell
        \b backspace
        \f form feed
        \n newline
        \r carriage return
        \t horizontal tab
        \v vertical tab
        \\ backslash
        \" double quote
        \' single quote
        \[ left square bracket
        \] right square bracket
    --]]
    ```
    * `-v`: tab vertical equivale a dar linefeed e continuar escrevendo na mesma coluna que se escreveria na linha original