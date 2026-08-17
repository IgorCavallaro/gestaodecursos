# Curso Livres — Controle de Presença (Nova Estação Church)

PWA para controle de presença de **múltiplos cursos** (não só o Livres), usando
uma única planilha Google Sheets como "banco de dados" via Google Apps Script.

## Como funciona a estrutura multi-curso

- Uma única planilha guarda **todos os cursos**.
- Cada curso ganha automaticamente 3 abas próprias: `Alunos_<id>`,
  `Presencas_<id>`, `Config_<id>` (o `<id>` é gerado a partir do nome do curso).
- Uma aba mestre **`Cursos`** lista todos eles: nome, duração em semanas, data
  de início, status (**Ativo** ou **Encerrado**).
- No app, a tela inicial agora é **"Meus Cursos"**: lista os cursos ativos e,
  numa aba separada, o **Histórico** (cursos encerrados — dados preservados,
  somente leitura). Um botão **+** cria um curso novo (nome, duração em
  semanas configurável, data de início opcional). Cada curso ativo tem um
  botão **"Encerrar curso"** (move pro Histórico) e cada encerrado tem
  **"Reabrir curso"** (volta a ficar ativo).
- O Curso Livres já existente foi migrado automaticamente para esse novo
  formato — nenhum dado de presença foi perdido.

## Estrutura entregue

```
livres-app/
├── index.html      → o app (interface + lógica), agora multi-curso
├── manifest.json   → configuração do PWA (nome, ícone, cor)
├── sw.js           → service worker (funciona offline / instalável)
├── icons/          → ícones do app (folha-gota NEC)
└── Code.gs         → backend Google Apps Script (cole no editor de Script)
```

---

## Parte 1 — Atualizar o "banco" no Google Sheets + Apps Script

Se você **já tinha configurado antes** (planilha + Apps Script do Curso Livres):

1. Abra a planilha de sempre → **Extensões → Apps Script**.
2. Apague todo o conteúdo de `Code.gs` e cole o conteúdo novo do arquivo
   **`Code.gs`** fornecido aqui (substitui o anterior).
3. No seletor de funções, escolha **`setupSheets`** → **Executar**.
   - Isso migra automaticamente as abas antigas `Alunos`/`Presencas`/`Config`
     para `Alunos_livres`/`Presencas_livres`/`Config_livres`, e cria a aba
     mestre **`Cursos`** já com o Curso Livres cadastrado como Ativo.
4. **Implantar → Gerenciar implantações → editar (ícone de lápis) → Nova
   versão → Implantar.** A URL `/exec` continua a mesma de sempre — não
   precisa mexer em nada no app por causa disso.

Se for a **primeira vez configurando** (nunca tinha rodado antes), siga os
mesmos passos 1–4: o `setupSheets` cria tudo do zero, incluindo o Curso
Livres com os 34 alunos já importados.

---

## Parte 2 — Publicar o PWA

Sem mudanças em relação a antes — suba `index.html`, `manifest.json`, `sw.js`
e a pasta `icons/` para o seu repositório do GitHub (Settings → Pages ativado).
Veja instruções detalhadas mais abaixo, na seção "Publicar o PWA".

---

## Parte 3 — Usando o painel

1. Abra o link do app. A tela inicial agora é **"Meus Cursos"**.
2. Toque no **Curso Livres** (ou em qualquer curso ativo) para entrar nas
   telas de Chamada / Painel / Alunos, exatamente como antes.
3. Use a seta **←** no canto superior esquerdo do cabeçalho para voltar à
   lista de cursos a qualquer momento.
4. Para criar um curso novo: na tela "Meus Cursos", toque no botão **+**,
   preencha nome, duração em semanas e (opcional) data de início.
5. Para encerrar um curso: dentro da lista "Ativos", toque em **"Encerrar
   curso"** no card do curso — ele some da aba Ativos e aparece em
   **Histórico**. Os dados de presença continuam salvos na planilha.
6. Para reabrir um curso encerrado: na aba **Histórico**, toque em
   **"Reabrir curso"**.

### Funciona offline?
Sim, parcialmente: se a conexão cair no meio da chamada, os toques em
Presente/Falta ficam guardados no celular e são reenviados automaticamente
assim que a internet voltar. O indicador verde/vermelho no canto superior
direito do app mostra o status da conexão com a planilha.

---

## Publicar o PWA

O app é 100% estático (HTML/CSS/JS), então pode ser hospedado em qualquer
lugar gratuito.

### Opção A — GitHub Pages (recomendado, grátis e simples)
1. Crie um repositório novo no GitHub (pode ser privado).
2. Suba os arquivos `index.html`, `manifest.json`, `sw.js` e a pasta `icons/`
   para a raiz (arraste a pasta `icons` inteira junto com os outros arquivos
   — não entre nela para subir os PNGs separados).
3. Em **Settings → Pages**, ative o GitHub Pages apontando para a branch
   `main` / pasta raiz.
4. Acesse a URL gerada (ex.: `https://seuusuario.github.io/curso-livres/`).

> **PWA exige HTTPS** para o Service Worker funcionar — GitHub Pages já serve
> em HTTPS por padrão.

---

## Dúvidas comuns

- **"Aluno não encontrado" ou dados não atualizam:** confira se a URL do app
  termina em `/exec` (não em `/dev`) e se a implantação está com acesso
  "Qualquer pessoa".
- **Quero resetar um curso específico:** apague manualmente as abas
  `Alunos_<id>`, `Presencas_<id>`, `Config_<id>` e a linha correspondente na
  aba `Cursos` — depois crie o curso de novo pelo app.
- **A URL do Apps Script mudou depois de uma atualização:** só acontece se
  você criar uma implantação **nova** em vez de editar a existente. Sempre
  prefira **Gerenciar implantações → editar → Nova versão**.
- **Quero importar mais alunos de uma vez:** use o botão **+** na aba Alunos
  dentro de um curso (adiciona um de cada vez), ou edite direto a aba
  `Alunos_<id>` na planilha, seguindo o mesmo padrão de colunas.

