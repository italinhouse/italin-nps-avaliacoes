# 🍝 Ital'in House — Sistema de Avaliações NPS

Sistema completo de coleta de avaliações NPS por loja, com painel administrativo protegido.

---

## 📁 Estrutura do projeto

```
/
├── index.html      → Página de avaliação do cliente (link único por loja)
├── admin.html      → Painel administrativo (protegido por senha)
└── README.md       → Este arquivo
```

---

## 🚀 Passo a passo de configuração

### 1. Criar projeto no Firebase (gratuito)

1. Acesse [console.firebase.google.com](https://console.firebase.google.com)
2. Clique em **"Adicionar projeto"**
3. Nome sugerido: `italin-nps`
4. Desative o Google Analytics (opcional)
5. Clique em **"Criar projeto"**

### 2. Ativar o Firestore

1. No menu lateral, clique em **Firestore Database**
2. Clique em **"Criar banco de dados"**
3. Selecione **"Iniciar no modo de produção"**
4. Escolha a região `southamerica-east1 (São Paulo)`
5. Clique em **"Ativar"**

### 3. Configurar regras de segurança do Firestore

No Firestore, vá em **Regras** e cole:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Qualquer pessoa pode criar avaliações (clientes)
    match /avaliacoes/{doc} {
      allow create: if true;
      allow read, update, delete: if false; // só via admin com SDK admin
    }
  }
}
```

> ⚠️ O painel admin usa a SDK pública com suas credenciais — isso é suficiente para uso interno.
> Para produção com muitos usuários, configure Firebase Authentication.

### 4. Obter as credenciais do Firebase

1. No Firebase, clique no ícone de engrenagem → **Configurações do projeto**
2. Role até **"Seus aplicativos"** → Clique em `</>` (Web)
3. Registre o app com qualquer nome
4. Copie o objeto `firebaseConfig`

### 5. Colar as credenciais nos arquivos

Abra `index.html` **e** `admin.html` e substitua o bloco marcado com `🔧`:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSy...",           // ← cole o seu
  authDomain: "italin-nps.firebaseapp.com",
  projectId: "italin-nps",
  storageBucket: "italin-nps.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

### 6. Alterar a senha do admin

Em `admin.html`, localize e edite:

```javascript
const ADMIN_USERS = {
  "admin": "italinhouse2026",  // ← troque pela sua senha
};
```

Você pode adicionar múltiplos usuários:
```javascript
const ADMIN_USERS = {
  "admin":    "senha_forte_aqui",
  "gerente":  "outra_senha",
};
```

### 7. Publicar no GitHub Pages

1. Crie um repositório no GitHub (ex: `italin-nps-avaliacoes`)
2. Faça upload dos arquivos `index.html` e `admin.html`
3. Vá em **Settings → Pages**
4. Em **Source**, selecione `main` → `/ (root)` → **Save**
5. Seu site estará em: `https://SEU_USUARIO.github.io/italin-nps-avaliacoes/`

---

## 🔗 Como gerar links para os clientes

O link de avaliação usa parâmetros de URL para identificar a loja:

```
https://italinhouse.github.io/italin-nps-avaliacoes/?loja=SP001&nome=Unidade%20Paulista
```

| Parâmetro | Obrigatório | Descrição |
|-----------|-------------|-----------|
| `loja`    | ✅ Sim       | ID interno da loja (ex: `SP001`) |
| `nome`    | Recomendado  | Nome exibível da loja (ex: `Unidade Paulista`) |
| `c`       | Opcional     | ID único do cliente (evita dupla avaliação) |

### Exemplos de links

```
# Loja básica
https://.../?loja=SP001&nome=Paulista

# Com ID de cliente (evita votar duas vezes)
https://.../?loja=SP001&nome=Paulista&c=CLI_98765

# Loja de outra cidade
https://.../?loja=RJ002&nome=Ipanema
```

### Integração com WhatsApp (sugestão de mensagem)

```
Olá, [Nome]! 😊

Obrigado por visitar a Ital'in House [Unidade]!
Sua opinião é muito importante para nós. Leva menos de 1 minuto:

👉 https://.../?loja=SP001&nome=Paulista&c=[ID_DO_PEDIDO]

Obrigado! 🍝
```

---

## 🔐 Segurança

| Recurso | Proteção |
|---------|----------|
| Painel admin | Senha definida no código (`ADMIN_USERS`) |
| Link do cliente | Só acessa a página de avaliação — sem acesso ao admin |
| Dupla votação | Bloqueada por `localStorage` + parâmetro `c` |
| Dados | Armazenados no Firebase com regras de leitura restrita |

> Para segurança mais robusta, use **Firebase Authentication** com e-mail/senha no admin.

---

## 📊 Funcionalidades do Painel Admin

- ✅ Login com usuário e senha
- ✅ KPIs: total de avaliações, NPS geral, média, avaliações hoje
- ✅ Barra visual de distribuição Detratores / Passivos / Promotores
- ✅ Tabela com todas as avaliações (data, loja, nota, perfil, comentário)
- ✅ Filtros por loja, perfil NPS e período
- ✅ Ordenação por coluna
- ✅ Aba "Por Loja" com ranking de NPS por unidade
- ✅ Exportação em CSV

---

## ❓ Dúvidas frequentes

**O cliente consegue acessar o painel admin pelo link de avaliação?**
Não. O link enviado ao cliente aponta apenas para `index.html`. O painel admin está em `admin.html` e exige senha.

**Os dados ficam seguros?**
Sim. O Firestore aceita gravação de qualquer origem (para os clientes avaliarem), mas leitura é restrita às credenciais do app — que só estão no arquivo `admin.html`.

**Posso hospedar em outro lugar além do GitHub Pages?**
Sim. Os arquivos são HTML/CSS/JS puros e funcionam em qualquer hosting estático (Netlify, Vercel, etc.).

---

*Sistema desenvolvido para Ital'in House © 2026*
