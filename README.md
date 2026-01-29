# 📚 Documentação – Beru Themes

Este repositório centraliza **toda a documentação técnica** relacionada aos projetos da organização **Beru**, com foco em temas, customização de editores e boas práticas para colaboração.

A ideia aqui é simples e pragmática: documentar **o que funciona**, **o que não funciona** e **por quê** — evitando tentativa e erro infinito.

---

## 📂 Estrutura do repositório

```text
docs/
├── Guia-VsCode/
│   └── Guia-VsCode.md
├── (futuro)
│   ├── Guia-Nvim/
```

Cada pasta representa um guia independente, escrito em Markdown, focado em um contexto específico (VS Code, Neovim, theming, etc.).

---

## 📘 Guias disponíveis

### 🔹 Guia VS Code

📁 `docs/Guia-VsCode/Guia-VsCode.md`

**Conteúdo:**

* Como funciona o sistema de temas do VS Code
* Limitações reais de scopes (ex: `px`, `!important`)
* Uso correto do *Inspect Editor Tokens*
* Por que certas mudanças não refletem, mesmo após build
* Processo recomendado para testar alterações

Esse guia nasceu de problemas reais enfrentados no projeto — não é teoria, é campo de batalha.

---

## 🧠 Filosofia da documentação

* Nada de “achismo”
* Nada de copiar tutorial sem testar

Tudo aqui deve ser:

* Reproduzível
* Explicável
* Justificável

Se algo não funciona, isso também merece ser documentado.

---

## 🤝 Contribuindo

Contribuições são bem-vindas via **Pull Request**.

**Regras básicas:**

* PR pequeno > PR gigante
* Explique o problema, não só a solução
* Se possível, inclua prints, scopes ou referências técnicas
* Melhorar documentação é tão valioso quanto código

Sugestões, correções e novos guias são incentivados.

---

## 🛠️ Projetos relacionados

* 🎨 **Beru Theme (VS Code)**
  [https://github.com/getBeru/beru-theme](https://github.com/getBeru/beru-theme)

* 🌙 **Beru Theme (Neovim)**
  [https://github.com/getBeru/beru-theme-nvim](https://github.com/getBeru/beru-theme-nvim)

* 🧠 **Organização Beru**
  [https://github.com/getBeru](https://github.com/getBeru)

---

## 👥 Maintainers

Este repositório é mantido por:

[@lucaspedruo](https://github.com/LucasPedruo)

**Beru Organization**
[https://github.com/getBeru](https://github.com/getBeru)

Os maintainers são responsáveis por:

* Revisar PRs
* Garantir consistência técnica
* Evitar regressões conceituais na documentação

---

## 📄 Licença

Este projeto segue a licença definida nos repositórios principais da Beru, salvo indicação contrária em guias específicos.
