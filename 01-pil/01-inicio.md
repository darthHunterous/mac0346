# 01 - Início
* Uso de `5.1` pois `LuaJit` não foi portado para a `5.3`
* `LuaVer` para gerenciar versões
* `luarocks` para plugins

### Executar script
* `lua hello.lua`

### Ler input como número
* `a = io.read("*number")`

### 1.1 - Chunks
* Sequência de declarações em Lua
* `;` é opcional, ajuda na legibilidade da separação de linhas
* `a = 1   b = a*2` é válido em Lua
* **Sair do interpretador interativo**: `os.exit()`
* **Executar dois arquivos em seguida**: `lua -lfile01 -lfile02`
* **Executar arquivos e invocar o interpretador em seguida**: `lua -i -lfile01 -lfile02`
* **Executar arquivo no interpretador**: `dofile("lib1.lua")`

### 1.2 - Variáveis Globais
* Variáveis globais não precisam de declaração
* Variáveis não inicializadas recebem `nil`
* Variáveis globais não costumam ser deletadas (se esse for o caso usar variáveis locais), do contrário atribuir `nil` para deletar
* Uma variável global só existe se e somente se tem um valor não `nil`

### 1.3 - Convenções Léxicas
* **Identificadores**: qualquer string de letras, dígitos ou underscore, sem começar com dígito
    * Identificadores começando com `_` e seguidos por letras maiúsculas são reservados para usos especiais em Lua
    * O identificador composto por apenas `_` server para identificar uma variável sem utilidade
* O que é tratado como `letter` depende do ambiente, ou seja, caracteres como `í` e `ç` podem ser incompatíveis com outros sistemas
* **Palavras reservadas**
    ```lua
    --[[
    and
    break
    do
    else
    elseif
    end
    false
    for
    function
    if
    in
    local
    nil
    not
    or
    repeat
    return
    then
    true
    until
    while
    --]]
    ```
    * Case sensitive: `and` é reservado porém `AND` não
* **Comentário**: `--`
* **Comentários de várias linhas**
    ```lua
    --[[
        comentario
        multiplas
        linhas
    --]]
    ```

### 1.4 - Interpretador Stand-Alone
* `lua.c` como código fonte ou `lua` como executável
* Ignora a primeira linha se começa com `#`
    * `#!/usr/local/bin/lua` ou `#!/usr/bin/env lua` permitem chamar o script diretamente sem invocar o interpretador de Lua
* **Uso**: `lua [options] [script[args]]`
* `-e`: permite código diretamente na linha de comando
    * `lua -e "print(42)"`
* **Exemplo**: `lua -i -l a.lua -e "x = 10"`
    * Carrega o arquivo, executa a atribuição e depois executa o prompt interativo
* **Alterar o indicador do prompt**: por padrão `>`, pode ser alterado com a variável global `_PROMPT`
    * `lua -i -e "_PROMPT=' lua> '"`
* `LUA_INIT`:se contém `@filename`, Lua carrega esse arquivo
    * Sem `@`, assume que é código e o executa
* **Argumentos**: `lua script a b c`
    * Disponíveis na variável global `arg`
    * O nome do script encontra-se em `arg[0]`, com elementos à direita como índices positivos e, à esquerda, negativos