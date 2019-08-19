# 03 - Expressões
* Expressões representam valores

### 3.1 - Operadores Aritméticos
* **Binários**: `+`, `-`, `*`, `/`
* **Unário**: `-` (negação)
* Todos operam em números reais
* Também há exponenciação `^`

### 3.2 - Operadores Relacionais
* Retornam `true` ou `false`
    * `<`, `>`, `<=`, `>=`, `==` (igualdade), `~=` (negação de igualdade)
* Se os valores têm tipos diferentes, são considerados diferentes
* `nil` só é igual a `nil`
* A comparação entre tabelas, userdata e funções se dá através da comparação das referências e não do conteúdo
* Operadores de ordem aplicados a string comparam alfabeticamente, de acordo com o locale configurado: `acai < açaí < acorde`
* `0 == "0"` é falso
* `"2" < "15"` é falso (ordem alfabética)

### 3.3 - Operadores Lógicos
* `and`, `or`, `not`
* Apenas `false` e `nil` são falsos, todo o resto é verdadeiro
* `and` retorna o primeiro argumento se falso e o segundo do caso contrário
* `or` retorna o primeiro argumento se verdadeiro e o segundo em caso contrário
* Avaliação em curto circuito: avaliam o segundo operando apenas se necessário
* `x = x or v` equivalente a `if not x then x = v end`
    * Atribui `v` para `x` se `x` ainda não foi definido
* `(a and b) or c` (parentêse redundante pois `and` tem precedência sobre `or`) equivale ao ternário de C: `a ? b : c`
    * Exemplo de máximo: `max = (x > y) and x or y`

### 3.4 - Concatenação
* `..`
    * Se qualquer operando for número, é convertido para string
    * Strings são imutáveis portanto o operador de concatenção cria uma nova sem modificar o conteúdo de seus operandos

### 3.5 - Precedência
* Ordem da maior à menor prioridade
    ```lua
    ^
    not -(unary)
    * /
    + -
    ..
    < > <= >= ~= ==
    and
    or
    ```
    * Operadores binários são associativos à esquerda (exceto exponenciação e concatenação)
        * Associatividade à esquerda: `a ~ b ~ c` é interpretado como `(a ~ b) ~ c` (avaliado da esquerda para direita)
        * Associatividade à direita: `a ~ b ~ c` é interpretado como `a ~ (b ~ c)` (avaliado da direita para esquerda)
    * Na dúvida usar parênteses

### 3.6 - Construtores de tabela
* Construtor para tabela vazia: `{}`
* Inicializar arrays (sequências ou listas)
    * `days - {"Sunday", "Monday", "Tuesday"}`
        * Inicializar sequencialmente, então `days[2] == "Monday"`
* Tabela como `record`: `a = {x=0, y=0}`, equivalente a `a = {}; a.x=0;; a.y=0`
* Tabelas podem implementar listas ligadas
    ```lua
    list = nil
    for line in io.lines() do
        list = {next=list, value=line}
    end
    ```
    * Armazena na ordem reversa
* Percorrer lista ligada
    ```lua
    l = list
    while l do
        print(l.value)
        l = l.next
    end
    ```
* Inicializações como records e listas podem ser misturadas
    ```lua
    polyline = {color="blue", thickness=2, npoints=4,
                  {x=0,   y=0},
                  {x=-10, y=0},
                  {x=-10, y=1},
                  {x=0,   y=1}
    }
    ```
    * `polyline[1]` a `polyline[4]` representam os records sequencialmente
* `{x=0, y=0}` é equivalente a `{["x"]=0, ["y"]=0}`
* `{"red", "green", "blue"}` equivalente a `{[1]="red", [2]="green", [3]="blue"}`
* Começar o índice do zero: `days = {[0]="Sunday", "Monday", "Tuesday"}`
    * Contra indicado pois muitas funções assumem arrays começando do um
* Vírgula opcional ao final do último elemento: `{[1]="red", [2]="green", [3]="blue",}`
    * Facilita fazer programas que geram tabelas por não precisar tratar o último elemento como especial
* `;` pode ser usado para delimitar seções diferentes de um construtor, geralmente separar lista de record
    * `{x=10, y-45; "one, "two", "three"}`