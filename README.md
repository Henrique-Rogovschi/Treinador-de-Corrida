# PISTA·10K — Setup Firebase + Vercel

## Passo 1 — Criar projeto no Firebase (5 min)

1. Acesse **console.firebase.google.com**
2. Clique "Criar projeto" → nome: `pista10k` → criar
3. No menu lateral: **Authentication** → "Começar"
   - Ative o provedor **Google**
   - Salve
4. No menu lateral: **Firestore Database** → "Criar banco de dados"
   - Escolha "Modo de produção"
   - Selecione uma região (ex: `southamerica-east1`)
   - Clique "Próxima" → "Criar"

### Regras do Firestore (segurança)
Em Firestore → Regras, cole:
```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```
Clique Publicar.

## Passo 2 — Pegar as credenciais do Firebase

1. No Firebase: clique na engrenagem ⚙️ → "Configurações do projeto"
2. Em "Seus apps", clique `</>` (Web)
3. Nome: `pista10k-web` → Registrar
4. Copie o objeto `firebaseConfig` que aparecer:
```js
{
  apiKey: "...",
  authDomain: "...",
  projectId: "...",
  storageBucket: "...",
  messagingSenderId: "...",
  appId: "..."
}
```

## Passo 3 — Colar no index.html

No arquivo `index.html`, procure a seção:
```js
const firebaseConfig = {
  apiKey:            "COLE_AQUI",
  ...
};
```
Substitua pelos valores copiados no passo 2.

## Passo 4 — Subir no GitHub

```bash
git init
git add .
git commit -m "pista10k firebase"
```
Ou: github.com → New repository → arrastar os arquivos.

## Passo 5 — Deploy no Vercel

1. Acesse **vercel.com** → Add New Project
2. Importe o repositório do GitHub
3. Em **Environment Variables**, adicione:
   - `ANTHROPIC_API_KEY` = sua chave em console.anthropic.com → API Keys
4. Clique Deploy

## Passo 6 — Autorizar o domínio no Firebase

1. Firebase → Authentication → Settings → Domínios autorizados
2. Clique "Adicionar domínio"
3. Cole o domínio do Vercel (ex: `pista10k.vercel.app`)

## Passo 7 — Adicionar à tela inicial do celular

**iPhone (Safari):**
1. Abra o link do Vercel no Safari
2. Ícone compartilhar ↑ → "Adicionar à Tela de Início"

**Android (Chrome):**
1. Abra no Chrome
2. Menu ⋮ → "Adicionar à tela inicial"

---

## Estrutura do projeto
```
pista10k-firebase/
├── index.html       ← app completo
├── api/
│   └── analyze.js   ← proxy Anthropic (chave segura no servidor)
├── vercel.json      ← configuração de rotas
└── README.md
```

## Dados salvos no Firebase
- Cada treino: `users/{uid}/treinos/{id}`
- Configurações: `users/{uid}/config/main`
- Cada usuário vê APENAS os seus próprios dados (regras de segurança)
