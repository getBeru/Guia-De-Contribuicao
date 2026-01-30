# Guia de Contribuição — Tema Beru (Neovim)

Este documento explica **como o tema funciona por dentro**, **o que pode ou não ser alterado**, e **como contribuir corretamente** sem cair em armadilhas comuns do Treesitter e Neovim.

A ideia é simples: qualquer melhoria deve vir via **Pull Request**, com clareza técnica e sem tentativa no escuro.

---

## 1. Visão geral do projeto

O Beru para Neovim é um **tema baseado em Lua** que usa:

- **Treesitter** para highlighting semântico
- **LSP Semantic Tokens** para informação contextual
- **Syntax tradicional** como fallback

Fluxo geral:

```
palette.lua → highlights.lua → Neovim
```

Tudo que você altera visualmente **passa pelo arquivo `highlights.lua`**.

---

## 2. Como funciona a definição de cores

### 2.1 Palette (paleta de cores)

As cores são definidas em `lua/beru/palette.lua`:

```lua
M.palette = {
    bg = "#101010",
    fg = "#BABABA",
    red = "#EF4444",
    -- ...
}
```

⚠️ **Regra de ouro:**
* Sempre use variáveis da palette (`p.red`, `p.blue`)
* NUNCA hardcode cores hexadecimais diretamente nos highlights
* Se precisar de uma nova cor, adicione na palette primeiro

---

## 3. Ordem das regras importa (muito)

Assim como no VS Code, no Neovim **a ordem importa**.

> A última regra que bate em um grupo de highlight **vence**.

Por isso:

* Regras genéricas (`@variable`, `@keyword`, `@function`) devem vir primeiro
* Regras específicas (`@variable.javascript`, `@keyword.storage`) devem vir depois
* Overrides de linguagens específicas devem estar no final

**Exemplo correto:**

```lua
["@variable"] = { fg = p.string_blue },  -- Geral
["@variable.javascript"] = { fg = p.string_blue },  -- Específico
["@variable.builtin"] = { fg = p.blue, italic = true },  -- Override
```

---

## 4. Como o Treesitter realmente funciona

Esse é o ponto mais importante do projeto.

### 4.1 Limitações do Treesitter

#### ❌ Não é possível diferenciar tags HTML por contexto

```jsx
<Card>  {/* componente customizado */}
  <img />  {/* tag HTML nativa */}
</Card>
```

No VS Code, `Card` é amarelo e `img` é azul.  
No Neovim, **ambos usam o mesmo scope** `@tag`.

**Solução:** Usamos `@constructor` para componentes customizados quando disponível.

#### ❌ Não é possível separar keywords por tipo

```javascript
const x = 1;  // keyword de storage
return x;     // keyword de controle
```

Ambos são `@keyword`. **Não tem como colorir só `const` de azul e `return` de vermelho.**

#### ❌ CSS/SCSS tem parsing limitado

```scss
font-size: 22px;  // "22px" é um token único
```

Não existe scope separado para `px`, `em`, `rem`, etc.  
Isso é uma limitação do parser Treesitter de CSS.

---

### 4.2 O que É possível fazer

✅ **Variáveis**
```lua
["@variable"] = { fg = p.string_blue }
["@variable.builtin"] = { fg = p.blue }
```

✅ **Propriedades**
```lua
["@property"] = { fg = p.string_blue }
```

✅ **Funções**
```lua
["@function"] = { fg = p.yellow }
["@function.call"] = { fg = p.yellow }
```

✅ **Keywords gerais**
```lua
["@keyword"] = { fg = p.red }
["@keyword.function"] = { fg = p.blue }
```

✅ **Tags e atributos**
```lua
["@tag"] = { fg = p.yellow }
["@tag.attribute"] = { fg = p.string_blue }
```

✅ **Por linguagem**
```lua
["@variable.javascript"] = { fg = p.string_blue }
["@variable.python"] = { fg = p.fg }
```

---

## 5. Como descobrir o scope correto

**SEMPRE faça isso antes de abrir um PR:**

### Método 1: Treesitter Playground

1. Instale `nvim-treesitter/playground`
2. Execute `:TSPlaygroundToggle`
3. Posicione o cursor no elemento
4. Veja o capture name na janela do playground

### Método 2: Inspect

1. Execute `:Inspect`
2. Posicione o cursor no elemento
3. Veja os highlight groups aplicados

### Método 3: InspectTree

1. Execute `:InspectTree`
2. Navegue pela árvore sintática
3. Encontre o node correspondente

**PRs sem verificação de scope real NÃO serão aceitos.**

---

## 6. Onde colocar regras novas

### 6.1 Regras genéricas

➡️ Colocar na seção `-- Treesitter` ou `-- Syntax (base)`

```lua
["@variable"] = { fg = p.string_blue },
["@function"] = { fg = p.yellow },
```

### 6.2 Regras específicas de linguagem

➡️ Colocar nas seções específicas:

```lua
-- ========================================================================
-- JavaScript/TypeScript
-- ========================================================================
["@variable.javascript"] = { fg = p.string_blue },
```

### 6.3 Regras de plugins

➡️ Colocar nas seções de plugins:

```lua
-- ========================================================================
-- Telescope
-- ========================================================================
TelescopeSelection = { fg = p.fg, bg = p.selection },
```

---

## 7. Como testar suas mudanças

### 7.1 Desenvolvimento local

```lua
-- Em seu init.lua
vim.opt.runtimepath:prepend("~/caminho/para/beru.nvim")
vim.cmd([[colorscheme beru]])
```

### 7.2 Recarregar o tema

```vim
:lua package.loaded['beru'] = nil
:lua package.loaded['beru.palette'] = nil
:lua package.loaded['beru.highlights'] = nil
:colorscheme beru
```

Ou simplesmente reinicie o Neovim.

### 7.3 Arquivos de teste

Crie arquivos de exemplo para cada linguagem que você modificou:

```
test/
├── test.js
├── test.tsx
├── test.css
└── test.lua
```

---

## 8. Padrão para Pull Requests

Todo PR **DEVE** conter:

### ✅ Obrigatório

1. **Descrição clara do problema visual**
   - "Variáveis em JavaScript estão brancas, deveriam ser ciano"

2. **Scope real usado**
   - Copiado do `:Inspect` ou Treesitter Playground
   - Exemplo: `@variable.javascript`

3. **Screenshots antes/depois**
   - Pelo menos 2 imagens comparando

4. **Arquivos de teste**
   - Se modificou JS, inclua um `test.js`

### ❌ Será rejeitado se

1. Tentar contornar limitações fundamentais do Treesitter
2. Não tiver screenshots
3. Não especificar o scope usado
4. Quebrar highlights existentes
5. Usar cores hardcoded ao invés da palette

---

## 9. Filosofia do tema

O Beru segue três princípios:

### 1. Respeitar limitações do Treesitter

Não tentamos "hackear" o sistema.  
Se algo não é possível, documentamos e seguimos em frente.

### 2. Consistência visual

Preferimos consistência a tentar imitar 100% o VS Code.  
Se uma cor não pode ser exata, escolhemos a mais próxima e mantemos em todo o tema.

### 3. Clareza técnica

Código limpo, bem comentado e organizado.  
Nada de "funciona mas não sei por quê".

---

## 10. Checklist antes de enviar PR

- [ ] Testei em um arquivo real da linguagem
- [ ] Verifiquei o scope com `:Inspect`
- [ ] Adicionei screenshots antes/depois
- [ ] Segui a estrutura de seções do arquivo
- [ ] Usei cores da palette
- [ ] Não quebrei highlights existentes
- [ ] Li este guia completamente

---

## 11. Exemplos de bons PRs

### ✅ Exemplo 1: Adicionar suporte a Python

```markdown
## Problema
Variáveis em Python estão brancas, deveriam ser ciano como em JS.

## Scope usado
`@variable.python` (verificado com `:Inspect`)

## Mudança
```lua
["@variable.python"] = { fg = p.string_blue },
["@parameter.python"] = { fg = p.string_blue, italic = true },
```

## Screenshots
[antes.png] [depois.png]

## Arquivo de teste
test/test.py
```

### ✅ Exemplo 2: Melhorar suporte a Rust

```markdown
## Problema
Macros em Rust não têm highlighting, ficam da cor padrão.

## Scope usado
`@function.macro.rust` (via Treesitter Playground)

## Mudança
```lua
["@function.macro.rust"] = { fg = p.purple },
```

## Screenshots
[antes.png] [depois.png]
```

---

## 12. Exemplos de PRs que serão rejeitados

### ❌ Exemplo 1: "Deixar só const azul"

```markdown
Quero deixar `const` azul mas `return` vermelho.
```

**Resposta:** Não é possível. Ambos são `@keyword` no Treesitter.  
Leia a seção 4.1 deste guia.

### ❌ Exemplo 2: "Colorir unidades CSS separadamente"

```markdown
Quero que `22` seja verde e `px` seja azul em `22px`.
```

**Resposta:** `22px` é um token único no Treesitter de CSS.  
Não é possível separar.

### ❌ Exemplo 3: PR sem screenshots

```markdown
Mudei umas cores.

```lua
["@variable"] = { fg = "#FF0000" }
```
```

**Resposta:** 
1. Sem screenshots
2. Sem explicação
3. Cor hardcoded (deveria usar palette)

---

## 13. Como adicionar suporte a um novo plugin

### Exemplo: Adicionar suporte ao plugin "X"

1. **Descubra os highlight groups do plugin**
   ```vim
   :so $VIMRUNTIME/syntax/hitest.vim
   ```

2. **Crie uma seção no arquivo**
   ```lua
   -- ========================================================================
   -- Plugin X
   -- ========================================================================
   PluginXNormal = { fg = p.fg, bg = p.bg_dark },
   PluginXSelected = { fg = p.fg, bg = p.selection },
   ```

3. **Teste com o plugin instalado**

4. **Documente no README**

---

## 14. Estrutura de commits

Use commits semânticos:

```
feat: adiciona suporte a Python
fix: corrige cor de variáveis em TypeScript
docs: atualiza README com novos plugins
style: reorganiza seção de CSS
refactor: simplifica lógica de highlights
```

---

## 15. Se algo "não muda"

Se você está tentando mudar algo e não funciona:

1. ✅ Verificou o scope com `:Inspect`?
2. ✅ O Treesitter está instalado e habilitado?
3. ✅ Você recarregou o tema?
4. ✅ Não há outro plugin sobrescrevendo?

Se depois disso ainda não funciona:

> **Provavelmente não existe scope específico para isso.**

Isso não é erro seu — é uma limitação do Treesitter.  
Documente e siga em frente.

---

## 16. Recursos úteis

### Documentação oficial

- [Neovim Treesitter](https://github.com/nvim-treesitter/nvim-treesitter)
- [Treesitter Highlight Groups](https://neovim.io/doc/user/treesitter.html#treesitter-highlight-groups)
- [LSP Semantic Tokens](https://neovim.io/doc/user/lsp.html#lsp-semantic-highlight)

### Ferramentas

- [nvim-treesitter/playground](https://github.com/nvim-treesitter/playground) — Inspeção visual
- [Inspect Commands](https://neovim.io/doc/user/treesitter.html#%3AInspect) — `:Inspect`, `:InspectTree`

### Temas de referência

- [Tokyo Night](https://github.com/folke/tokyonight.nvim) — Excelente estrutura
- [Catppuccin](https://github.com/catppuccin/nvim) — Ótima organização
- [Gruvbox Material](https://github.com/sainnhe/gruvbox-material) — Suporte extensivo

---

## 17. Conclusão

Melhorias são **muito bem-vindas**.  
Tentativas no escuro, não.

Se você leu este guia até aqui, está pronto para contribuir! 🚀

Qualquer dúvida, abra uma issue primeiro antes de fazer o PR.

---

**Bem-vindo ao projeto!**

Co-authored-by: ComunidadeFullDev <contato@fulldev.com.br>
