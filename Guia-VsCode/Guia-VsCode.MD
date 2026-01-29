# Guia de Contribuição — Tema Beru (VS Code)

Este documento explica **como o tema funciona por dentro**, **o que pode ou não ser alterado**, e **como contribuir corretamente** sem cair em armadilhas comuns do VS Code.

A ideia é simples: qualquer melhoria deve vir via **Pull Request**, com clareza técnica e sem tentativa no escuro.

---

## 1. Visão geral do projeto

O Beru é um **tema para VS Code** baseado em **YAML + build script** que gera o JSON final consumido pelo editor.

Fluxo geral:

```
YAML (com anchors) → build.js → theme.json → VS Code
```

Tudo que você altera visualmente **passa primeiro pelo YAML**.

---

## 2. Como funciona a definição de cores

### 2.1 Anchors (&) e aliases (*)

As cores são definidas usando **YAML anchors**.

Exemplo:

```yaml
- &YELLOW '#eeba61'
```

Uso:

```yaml
foreground: *YELLOW
```

⚠️ **Regra de ouro:**

* Se um alias (`*COLOR3`) for usado antes do anchor (`&COLOR3`) existir
* Ou se uma string não for fechada (`'#EF4444`)

👉 o build **quebra inteiro**.

Sempre revise aspas.

---

## 3. Ordem das regras importa (muito)

O VS Code **não usa especificidade**, só **ordem**.

> A última regra que bate em um token **vence**.

Por isso:

* Regras genéricas (`support`, `variable`, `keyword`) sobrescrevem regras específicas
* Ajustes finos devem ficar **no final do arquivo**

---

## 4. Como o VS Code realmente tokeniza CSS / SCSS

Esse é o ponto mais importante do projeto.

### 4.1 Valores numéricos

```scss
font-size: 22px;
```

No VS Code isso é:

```
22px → constant.numeric.scss
```

❌ NÃO existe token separado para `px`
❌ NÃO existe token separado para `em`, `rem`, `%`

👉 **Não é possível colorir apenas a unidade.**

Todos os temas famosos (Dracula, Omni, One Dark) funcionam assim.

---

### 4.2 !important

Em SCSS, `!important`:

* não tem scope consistente
* às vezes cai em `meta.property-value.scss`
* às vezes herda a cor padrão

👉 **Não é confiável estilizar `!important` via tema.**

Se um PR tentar isso, será recusado.

---

## 5. O que É possível estilizar (e vale PR)

Esses scopes funcionam de verdade:

* Propriedades CSS

  * `entity.other.attribute-name.css`
* Valores numéricos (inclui px)

  * `constant.numeric.css`
  * `constant.numeric.scss`
* Cores hex

  * `constant.other.color.css`
* Funções

  * `support.function.css` (`calc`, `var`)
* Seletores

  * `meta.selector`

---

## 6. Como descobrir o scope correto

Antes de abrir PR:

1. `Ctrl + Shift + P`
2. **Developer: Inspect Editor Tokens and Scopes**
3. Clique no token
4. Copie o scope real

PRs sem essa verificação **não serão aceitos**.

---

## 7. Onde colocar regras novas

### Regras específicas (CSS, SCSS, TS, etc)

➡️ **Sempre no final do arquivo**, após regras genéricas como:

* Language Built-ins
* Variables
* Keywords

Isso evita sobrescrita invisível.

---

## 8. Build e validação

Comandos:

```bash
npm install
npm run build
```

Se o build quebrar:

* verifique **anchors inexistentes**
* verifique **aspas não fechadas**
* YAML **não aponta o erro onde ele nasce**

Leia o stacktrace com calma.

---

## 9. Padrão para Pull Requests

Todo PR deve conter:

* O **problema visual** que resolve
* O **scope real** usado (copiado do Inspect Tokens)
* Screenshot antes/depois
* Nenhuma tentativa de estilizar `px` isolado ou `!important`

PR sem isso será fechado sem discussão.

---

## 10. Filosofia do tema

O Beru segue três princípios:

1. **Sem hacks frágeis**
2. **Respeitar limites do VS Code**
3. **Clareza visual acima de modismo**

Se a mudança viola um desses pontos, ela não entra.

---

## 11. Conclusão

Se algo "não muda":

* provavelmente **não existe scope**
* ou está sendo sobrescrito

Isso não é erro do colaborador — é o motor do VS Code.

Melhorias são bem-vindas.
Tentativas no escuro, não.

Bem-vindo ao projeto 🚀
