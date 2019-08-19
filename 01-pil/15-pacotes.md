# 15 - Pacotes
* Não há mecanismo explícitos para pacotes em Lua mas podem ser representados com uma tabela, assim como bibliotecas básicas
    ```lua
    complex = {}
    
    function complex.new (r, i) return {r=r, i=i} end
    
    -- defines a constant `i'
    complex.i = complex.new(0, 1)
    
    function complex.add (c1, c2)
      return complex.new(c1.r + c2.r, c1.i + c2.i)
    end
    
    function complex.sub (c1, c2)
      return complex.new(c1.r - c2.r, c1.i - c2.i)
    end
    
    function complex.mul (c1, c2)
      return complex.new(c1.r*c2.r - c1.i*c2.i,
                         c1.r*c2.i + c1.i*c2.r)
    end
    
    function complex.inv (c)
      local n = c.r^2 + c.i^2
      return complex.new(c.r/n, -c.i/n)
    end
    
    return complex
    ```
    * `complex = {}` pode ser substituído por `local P = {}`, trocando o prefixo de cada função adequadamente
        * Depois atribui-se `P` a global `complex` com `complex = P`
    * `return complex` permite uma maior gama de alternativas para manipular o pacote

### 15.2 - Privacidade
* Para ter nomes privados em um pacote, pode-se definir como variáveis locais, garantindo que apenas o pacote consiga usar
* Como alternativa podemos declarar todas funções dentro de um pacote como `local` e depois adicionar na tabela do pacote as que desejamos exportar
    ```lua
    complex = {
        new = new,
        add = add,
        sub = sub,
        mul = mul,
        div = div,
    }
    ```
    * Assim, não precisamos do prefixo em cada função

### 15.3 - Pacotes e Arquivos
* `require "complex"`

### 15.4 - Tabela Global
* Mudar o ambiente do pacote para usar outro exclusivo
* Com `local P = {}`, depois `complex = P`, usar `setfenv(1, P)`
    * Permite chamar as funções sem prefixos
    * Não há diferenças entre chamar uma função exporta e outra privada
* Porém ao mudar o ambiente, perde-se acesso a todas variáveis globais, podendo-se usar herança com `setmetatable(P, {__index = _G})` antes de usar `setfenv()`
    * Outra alternativa é declarar uma variável local para o ambiente antigo antes de `setfenv()` com `local _G = _G`
        * `_G` precisa ser usado como prefixo
    * Também pode-se fazer uma seção de imports para declarar como locais apenas as funções necessárias como `local sqrt = math.sqrt` e `local io = io`

### 15.5 - Outros Recursos
* Mais de um pacote pode ser definido no mesmo arquivo, desde que seja fechado em um bloco `do`
* Fora do pacote, pode-se definir nomes locais a funções usadas com frequência
* Pode-se dar um nome local mais curto ao pacote para escrever menos
* Função para desempacotar um pacote, colocando todos nomes no namespace global
    ```lua
    function openpackage(ns)
        for n,v in pairs(ns) do _G[n] = v end
    end

    openpackage(complex)
    c1 = mul(new(10, 20), i)
    ```
* **Autoload**: só carrega uma função se ela for utilizada pelo programa. Cria uma tabela vazia e configura o metamétodo `__index` para fazer o autoload
    * Definir uma tabela no pacote principal para descrever o local de cada função
    * Definir o metamétodo no pacote em questão
        ```lua
        pack1 = {}

        setmetatable(pack1, {__index = function (t, funcname)
            local file = location[funcname]
            if not file then
                error("package pack1 does not define " .. funcname)
            end
            assert(loadfile(file))()  -- carrega e executa a definição
            return t[funcname]  -- retorna a função
        end})

        return pack1