# Questionário — configuração e publicação

Arquivos:
- `index.html` → o questionário público (o que você manda pras pessoas)
- `admin.html` → o painel só seu, pra ver as respostas (com login)
- `manifest.json` + `sw.js` + `icon-192.png` + `icon-512.png` → fazem o `admin.html` ser instalável
- `firestore.rules` → regras de segurança do banco de dados
- `README.md` → este guia

Nada disso precisa de build, npm, servidor próprio, etc. É tudo estático.

---

## 1. Criar o projeto no Firebase

1. Acesse https://console.firebase.google.com
2. **Criar projeto** → dê um nome (ex: `meu-questionario`) → pode desativar o Google Analytics.
3. **Compilação → Firestore Database** → **Criar banco de dados** → modo **produção** → região mais próxima (ex: `southamerica-east1`).

## 2. Pegar as credenciais do app

1. ⚙ **Configurações do projeto** → **Seus apps** → ícone `</>` (Web) → registrar app.
2. Copie o bloco `firebaseConfig = { ... }` que aparece.

## 3. Colar as credenciais nos DOIS arquivos

Abra `index.html` **e** `admin.html`. Em ambos, perto do final, tem:

```js
const firebaseConfig = {
  apiKey: "SUA_API_KEY",
  authDomain: "SEU_PROJETO.firebaseapp.com",
  projectId: "SEU_PROJETO",
  storageBucket: "SEU_PROJETO.appspot.com",
  messagingSenderId: "SEU_SENDER_ID",
  appId: "SEU_APP_ID"
};
```

Substitua pelos valores reais nos dois arquivos (é a mesma config nos dois — um projeto Firebase só).

## 4. Criar seu login para o painel de respostas

O `admin.html` só abre pra quem estiver logado. Você precisa criar essa conta uma vez:

1. Console do Firebase → **Compilação → Authentication** → **Começar**.
2. Aba **Sign-in method** → ative o provedor **E-mail/senha**.
3. Aba **Users** → **Add user** → coloque o e-mail e a senha que *você* vai usar pra entrar no painel.

Guarde esse e-mail/senha — é o que você vai digitar no `admin.html`.

## 5. Aplicar as regras de segurança

**Pelo Console (mais simples):**
1. **Firestore Database** → aba **Regras**.
2. Apague tudo e cole o conteúdo de `firestore.rules`.
3. **Publicar**.

Essas regras garantem: qualquer um pode enviar uma resposta pelo `index.html`, mas só quem estiver logado (você, pelo `admin.html`) consegue ler.

## 6. Subir para o GitHub (opcional, mas recomendado como backup do código)

```bash
cd questionario
git init
git add .
git commit -m "questionário + painel"
git branch -M main
git remote add origin https://github.com/SEU_USUARIO/SEU_REPO.git
git push -u origin main
```

## 7. Publicar com Firebase Hosting

```bash
npm install -g firebase-tools
firebase login
firebase init hosting
```
Quando perguntar a pasta pública, aponte pra onde estão os arquivos (`.` se estiver na raiz). Diga **não** pra "single-page app".

```bash
firebase deploy --only hosting
```

Você recebe uma URL tipo `https://seu-projeto.web.app`. Isso te dá **dois links**:
- `https://seu-projeto.web.app/` → o questionário público, manda pra quem quiser
- `https://seu-projeto.web.app/admin.html` → o seu painel de respostas, só você usa

## 8. Instalar o painel como um app (opcional)

O `admin.html` foi feito pra ser "instalável", como um app de verdade:

**No celular (Android/Chrome):** abra o link do admin → menu (⋮) → **Instalar app** (ou **Adicionar à tela inicial**).
**No computador (Chrome/Edge):** abra o link do admin → ícone de instalação na barra de endereço (ou menu → **Instalar [nome do app]**).

Depois disso ele abre como um appzinho separado, com ícone próprio — você clica e cai direto na tela de login, sem precisar abrir o navegador. Só funciona depois de publicado (precisa ser https).

## 9. Como ver as respostas no dia a dia

Duas formas, escolha a que preferir:
- **Pelo painel (`admin.html`)**: mais bonito, filtra e organiza os campos, dá pra abrir/fechar cada resposta clicando.
- **Pelo Console do Firebase**: Firestore Database → aba **Dados** → coleção `respostas`. Mais "cru", mas sempre funciona.

---

## Personalizar as perguntas

As perguntas estão em `index.html`, dentro dos blocos `<div class="step" data-step="N">`. Se adicionar ou remover um campo, lembre de também ajustar:
- o objeto `payload` dentro do `<script>` do `index.html` (pra ele ser salvo)
- o objeto `FIELD_LABELS` dentro do `<script>` do `admin.html` (pra ele aparecer no painel)
