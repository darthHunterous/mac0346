* luarocks para plugins

* nil em Lua para apagar dados da memória
* bool
    * Zero não é falso em Lua
* number: inteiros e floats
    * Divisão de inteiros produz floats
* string
    * String de várias linhas com [[
        ```lua
        t = [[
            blah blah
            blah
        ]]
        ```
* table: dicionarios chave valor
    * `t = {}`
    * São comparadas pela referência e não por conteúdo
    * Criar chave: `t[k] = v`
    * Acesso com `t[k]`
    * `t = { |2| = 42, foo = 1337 }`
        * Mais rápido pois já se sabe quanto alocar de memória ao invés de fazer dinamicamente (mais lento)
* function
* userdata: se refere a algo que está fora da máquina virtual do Lua
* thread: engana, não é multithreaded, isto é uma co-rotina
* Lua começa indexando vetores de 1
* `hero = { money = 9999, hp = 100, power = 20 }`
* `ipairs` itera considerando como sequência enquanto `pairs` não
    * `t = { 10, 20, 30 }`
    `* `for i,v in ipairs(t) do prit(i, v) end`
        * Imprime 1  10, 2  20, 3  30
    * `t["0"] = 0`
        * `for k,v in pairs(t) do print(k,v) end`
            * Imprime desordenado pois hash não tem ordem
                * 1 10, 2 20, 3 30, 0 0
* Apenas false e nil são falsos em Lua
* `set = {}`
    * `set["foo"] true`

```lua
-- comentario em Lua
local f = function()
    print("foo")
end

f()

f = function()
    print("foo")
end

-- apenas essa sintaxe permite recursão
function g()
    print("bar")
end
```
* Tail call em recursão

** Pseudo métodos
```lua
t = {}
t.foo = function() print("oi") end
t.foo()

-- em lua não se tem acesso direto ao self, precisa passar
t.foo = function(self) print("oi", self) end

function t.bar(self)
    print("tchau", self)
end
t.bar()

function t:baz()
    print(self)
end
t:baz()
-- para chamada e definição de métodos usa-se :
-- dois pontos fornece acesso ao self automaticamente
-- permite trabalhar orientado a objeto em Lua

--[[
    comentario
    em
    multiplas
    linhas
]]
```

```lua
a, b = 1, 2
print(a, b)
-- 1    2

a, b = b, a
print(a, b)
-- 2    1

function foo()
    return 2, 4
end

c, d = foo()
print(c, d)
-- 2    4

function foo(...)
    return ...
end
-- retorna vários argumentos
```

* Todas variáveis declaradas normalmente são globais em Lua
* Lua permite a troca de valores de variáveis sem auxiliar
    * Em C magia negra do XOR

```lua
if x > 10 then
    print("maior")
elseif x < 10 then
    print("menor")
else
    print("igual")
end

c = 1
while c <= 10 do
    print(c)
    c = c + 1
end

c = 1
repeat
    print(c)
    c = c + 1
until c > 10

-- de 1 ate 10
for i=1,10 do
    print(i)
end
-- continua enquanto menor ou IGUAL ao máximo, ou seja engloba 10

for i=1,10,2 do
    print(i)
end
-- por passos de dois

t = {}
for i=1,10 do
    t[i] = i*2
end
for i=1,10 do
    print(t[i])
end

for i,v in ipairs(t) do
    print(i, v)
end
-- imprime de 1 a 10 sequencialmente

t.x = 42
t.y = 1337
-- imprime tambem oq nao eh sequencial
for k,v in pairs(t) do
    print(k, v)
end

for i=1,#t do
    print(i, t[i])
end
-- so funciona com a sequencia na tabela
-- senao comportamento indefinido
-- ter certeza que nao ha buracos na sequencia da tabela
-- para manter sem buracos, usar falso para remover e deixar la ao inves de nil

-- # serve para saber o tamanho de string e de tabela
-- não é constante, faz busca binária log n, entao armazenar em variavel
```

* Não existe caracter puro, apenas strings, portanto é necessário usar uma função para ver uma determinada posição da string

* `print(print)`

* Operador de potência
    * `x = 2 ^ 0.5`
* Operador de diferença
    * `print(5 ~= 4)`

```lua
print("asd" == "asd")
-- true

print(true or false)
print(true and false)
print(not false)

print("asd" or false)
-- true

function foo()
    print("foo")
    return false
end

function bar()
    print("bar")
    return true
end

print(foo() or bar())
-- foo
-- bar
-- true

print(bar() or foo())
-- bar
-- true

* Módulos
* Arquivo example.lua
```lua
local M = {}

function M.foo()
    print("yeah")
end

return M
```
* Arquivo main.lua
```lua
local M = require "example"

M.foo()
```

* Toda variável declarada sem local é global
    * Ideal é sempre declarar como local

* Tabela _G com todos os dados do programa

* Não usar função module, criar um arquivo externo e dar require

```lua
-- funcao unpack
t = {1, 2, 3}
x,y, z = unpack(t)
print(x, y, z)
-- 1    2    3
```

### Aula 03 - Lua parte 02
```lua
local function foo(x)
    print(x)
end

foo(42)
-- Imprime corretamente

setfenv(foo, {})
-- Muda o ambiente da tabela global _G para uma tabela vazia
foo(42)
-- Portanto aqui dá erro

getfenv(foo) == _G
-- Retorna ao ambiente da tabea global _G

-- Mudar o env se refere à limitar oq o desenvolvedor pode mexer ao fazer plugins ou lidar com código já feito
-- Evitando que ele cometa erros ou altera o que não deveria

-- Exemplo de código malicioso
local chunk = loadfile("data.lua")
local data = chunk()
-- Pode alterar os dados com código injetado
-- A opção para evitar isso é usar:
setfenv(chunk, {})
```

```lua
function mistake(x)
    t[1] = x
    return x + x
end

local ok, result = pcall(mistake, x)

if not ok then
    print(result)
else
    print("resposta: " .. result)
end

return error("duplicate item detected")
-- Adiciona-se return para deixar claro que não executará nenhuma outra linha por error ter parado o programa
```

```lua
local function takeDamage(amount)
    assert(amount > 0, "negative damage")
end

takeDamage(10)
takeDamage(-10)
-- Não queremos que o personagem possa levar dano negativo
```

```lua
local t = { 0, 0 }

local meta = {}

setmetatable(t, meta)

function meta:_add(other)
    return { self[1] + other[1], self[2] + other[2] }
end
-- Permite sobre-escrever o método de soma, adicionando opção de somar coisas que não são números
local x = t + t

print(unpack(x))

-- O metamétodo é sempre o plano b

function meta:_call()
    print(unpack(self))
end

t()
-- Imprime a tabela t

function meta:_index(key)
    if key == 'x' then
        return rawget(self, 1)
    end
    elseif key == 'y' then
        return rawget(self, 2)
    end
end
print(t.x)
```

```lua
local Vec = {}

Vec._index = Vec

function Vec.new(x, y)
    local new_vec = { x, y }
    setmetatable(new_vec, Vec)
    return new_vec
end

function Vec:print()
    print("(" .. self[1] .. ',' .. self[2] .. ')')
end

return Vec
```

### Coroutine
* Não são threads pois não executam em paralelo e precisam ceder o controle umas às outras
```lua
local yield = coroutine.yield

local function routine()
    print(1)
    print(2)
    yield()
    print(3)
end

local co = coroutine.create(routine)

print("before")
coroutine.resume(co)
print("after")
coroutine.resume(co)
print("after after")

-- before
-- 1
-- 2
-- after
-- 3
-- after after

-- Útil para jogos em onda, que esperam o spawn
local function spawnEnemy(name)
    print("A wild" .. name .. "has appeared")
end

local function waves()
    for _ = 1,10 do
        spawnEnemy("slime")
        yield()
    end
    yield(true)
    for _ = 1,8 do
        spawnEnemy("slime")
        yield()
    end
    for _ = 1,4 do
        spawnEnemy("orc")
        yield()
    end
    yield(true)
end

local wave_co = coroutine.wrap(waves)

print("first wave")
while not wave_co() do print("wait") end
print("second_wave")
while not wave_co() do print("wait") end

-- corrotinas são muito usadas em web pois é necessário esperar requisições http terminarem
```

### Método `collectgarbage`
* Muitas vezes o jogo está lagando periodicamente por ter acumulado lixo por 200 frames, chamar esse método a cada 2 ou 3 frames ajuda a aliviar as operações
