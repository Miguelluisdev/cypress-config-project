# 🚀 Setup Inicial – Projeto Cypress + TypeScript + Mochawesome + CI/CD

Este guia contém **todo o passo a passo completo** para iniciar um projeto moderno de automação com:

* **Cypress** → [https://www.cypress.io/](https://www.cypress.io/)
* **TypeScript** → [https://www.typescriptlang.org/](https://www.typescriptlang.org/)
* **Mochawesome** → [https://github.com/adamgruber/mochawesome](https://github.com/adamgruber/mochawesome)
* **GitHub Actions (CI/CD)** → [https://docs.github.com/actions](https://docs.github.com/actions)
* **Node.js** → [https://nodejs.org/](https://nodejs.org/)
 * caso for testar api >>
* **Cypress Plugin API (cypress-plugin-api)** → [https://github.com/filiphric/cypress-plugin-api](https://github.com/filiphric/cypress-plugin-api)


## 🔧 1. Inicializar o projeto

```bash
npm init -y
```

---

## 🔧 2. Instalar Cypress

```bash
npm install cypress --save-dev
```

```bash
npx cypress open
```

---

## 🔧 3. Instalar TypeScript

```bash
npm install typescript @types/node --save-dev
npx tsc --init
```

### 📌 tsconfig.json recomendável

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "lib": ["ES2020", "DOM"],
    "module": "CommonJS",
    "moduleResolution": "Node",
    "types": ["cypress"],
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  },
  "include": ["cypress/**/*.ts"]
}
```

---

## 🔧 4. Instalar Mochawesome

```bash
npm install --save-dev mochawesome mochawesome-merge mochawesome-report-generator
```

---

## 🔧 5. Configurar o Cypress

**cypress.config.js**

```js
const { defineConfig } = require("cypress");

module.exports = defineConfig({
  reporter: "mochawesome",
  reporterOptions: {
    reportDir: "cypress/reports/mochawesome",
    overwrite: true,
    html: true,
    json: true
  },
  e2e: {
    baseUrl: "https://seu-site.com",
    specPattern: "cypress/e2e/**/*.spec.ts",
    supportFile: "cypress/support/e2e.ts",
    setupNodeEvents(on, config) {
      return config;
    }
  }
});
```

---

## 🔧 6. Scripts no package.json

```json
{
  "scripts": {
    "test": "cypress run",
    "merge": "mochawesome-merge cypress/reports/mochawesome/*.json > cypress/reports/mochawesome/mochawesome.json",
    "generate": "marge cypress/reports/mochawesome/mochawesome.json --reportDir cypress/reports/final --overwrite",
    "report": "npm run merge && npm run generate"
  }
}
```

---

## 🔧 7. Estrutura recomendada

```
cypress/
  e2e/
    login/
      login.spec.ts
    checkout/
      checkout.spec.ts

  fixtures/
    users.json

  support/
    commands.ts
    e2e.ts

cypress.config.js
tsconfig.json
```

Simples, clean e funcional.

---

# ⭐ Vantagens reais de usar Cypress com TypeScript

A maioria das pessoas fala superficialmente “TypeScript é melhor porque tipa o código”.

Mas na automação, as **VANTAGENS PRÁTICAS** são muito maiores:

---

## 🚀 1. Autocompletar extremamente preciso

Com JavaScript:

```js
cy.get("")
```

Com TypeScript:

```ts
cy.get("").click().should("be.visible")
```

---

## 🔥 2. Prevenção de erros bobos

JavaScript:

```js
cy.get("#btn").clik();
```

TypeScript:

```ts
cy.get("#btn").clik();
// ❌ Property 'clik' does not exist.
```

---

## 🛡️ 3. Comandos customizados tipados

```ts
Cypress.Commands.add("login", (user: string, pass: string) => {})
```

E depois:

```ts
cy.login("admin", "123");
```

---

## 📚 4. Melhor organização e arquitetura de testes

Exemplo:

```ts
interface User {
  email: string
  password: string
}
```

Uso:

```ts
const user: User = require("../../fixtures/users.json")
cy.login(user.email, user.password)
```

---

## 🚀 5. Projetos mais profissionais (empresas exigem)

Mais de 80% dos projetos modernos usam:

✔️ Cypress
✔️ TypeScript
✔️ CI/CD
✔️ Mochawesome ou Allure

---

# ❗ Erro comum: "No inputs were found"

O erro significa:

👉 o tsconfig está correto,
👉 mas não existe nenhum arquivo TypeScript no caminho `cypress/**/*.ts`.

---

## ✅ Como resolver (3 opções)

### ✔️ Opção 1 — Criar pelo menos um arquivo .ts

`cypress/e2e/example.spec.ts`

```ts
describe("Example", () => {
  it("works", () => {
    expect(true).to.equal(true)
  })
})
```

---

### ✔️ Opção 2 — Criar o arquivo padrão support

Renomear:

```
cypress/support/e2e.js → e2e.ts
```

E adicionar:

```ts
/// <reference types="cypress" />
```

---

## ❗ Outro erro comum: supportFile vazio

O Cypress **não aceita arquivo vazio**.

Solucione colocando:

```ts
/// <reference types="cypress" />

// Arquivo de suporte do Cypress
```

E reinicie:

```bash
npx cypress open
```

---

# ⚙️ Configuração de CI/CD – GitHub Actions

Arquivo: `.github/workflows/cypress.yml`

```yaml
name: Cypress Tests

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  cypress-run:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Setup Node
        uses: actions/setup-node@v3
        with:
          node-version: 18

      - name: Install dependencies
        run: npm install

      - name: Run Cypress tests
        run: npm run test

      - name: Merge Mochawesome reports
        run: npm run merge

      - name: Generate HTML report
        run: npm run generate

      - name: Upload Mochawesome report
        uses: actions/upload-artifact@v3
        with:
          name: mochawesome-report
          path: cypress/reports/final
```

#  3. Estruture com lógica (e um pouco de OCD, por que não?)

Automação boa é **automação organizada**.

E organização começa com **clareza**, não com complexidade.

A ideia é simples: cada coisa tem seu lugar.
Nada de arquivos soltos, nada de zona, nada de colocar tudo dentro de uma pasta “tests” achando que está organizado.

Monte algo assim:

```
cypress/
  e2e/
    login/
      login.spec.ts

    checkout/
      checkout.spec.ts

  fixtures/
    users.json

  support/
    commands.ts
    e2e.ts
```

### 📌 O conceito principal: **cada módulo tem sua casa**

* **Quer testar login?**
  → Vá até `e2e/login/`.

* **Quer testar checkout?**
  → Vá até `e2e/checkout/`.

* **Quer editar dados estáticos?**
  → Vá até `fixtures/`.

* **Quer criar comandos customizados e helpers globais?**
  → Vá até `support/`.

Isso reduz confusão, elimina arquivos gigantes e facilita achar qualquer coisa em segundos.

---

# 🧠 Por que isso é tão importante?

## ✔️ 1. Teste organizado = manutenção barata

Se amanhã o projeto tiver 500 testes, você ainda acha tudo sem sofrimento.

## ✔️ 2. Cada área é independente

Você nunca mistura cenário de login dentro de checkout, nem o contrário.

## ✔️ 3. Ajuda no onboarding

Quando outra pessoa entrar no projeto ela entenderá a estrutura na primeira olhada.

## ✔️ 4. Facilita CI, fixtures, mocks, comandos…

Cada parte fica isolada e simples de manipular.

---

# 🚫 E o mais importante: **nunca crie dependência entre testes**

Isso aqui é uma regra de ouro.

❌ Nada de usar o teste A para preparar o teste B.
❌ Nada de “login no primeiro teste e aproveitar pros outros”.
❌ Nada de usar ID gerado em outro teste.

Se um teste depende de outro, os dois perdem o sentido.

Cada teste deve:
✔️ começar sozinho
✔️ preparar seu próprio contexto
✔️ não depender da ordem de execução
✔️ não quebrar porque outro teste falhou
