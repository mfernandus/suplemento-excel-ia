# Assistente IA — Suplemento de Excel (Next Editorial)

Suplemento de painel (task pane) que conecta o Excel à API da OpenAI usando **a sua própria chave**.
Dois modos de uso:

- **Por célula** — aplica a instrução a cada célula selecionada e escreve o resultado na coluna imediatamente à direita. Útil para normalizar nomes, classificar, traduzir, gerar legendas em lote, padronizar telefones etc.
- **Resumo** — envia a seleção inteira como contexto e devolve uma resposta única no painel (sem alterar a planilha). Útil para análise e síntese de dados.

A chave fica salva **apenas no navegador** (localStorage) e só é enviada à OpenAI.

---

## Arquivos

- `taskpane.html` — interface + lógica (arquivo único, autocontido).
- `manifest.xml` — descreve o suplemento para o Excel.

---

## Passo 1 — Hospedar o `taskpane.html` em HTTPS

O Office exige **HTTPS**. Opções simples:

- **GitHub Pages** (grátis): suba o `taskpane.html` num repositório, ative Pages e use a URL gerada.
- Qualquer hospedagem que você já use (o mesmo lugar do seu suplemento do Word serve).
- Para testar localmente: sirva a pasta com um servidor HTTPS local (ex.: o servidor do projeto Yeoman, ou `npx http-server -S`).

Anote a URL final do arquivo, por exemplo:
`https://meudominio.com/taskpane.html`

## Passo 2 — Editar o `manifest.xml`

Substitua **todas** as ocorrências de `https://SEU-DOMINIO` pela sua URL real.
Se não for usar ícones próprios, pode apontar os ícones para qualquer PNG público
(16, 32, 80 px) ou hospedar os seus.

> O `<Id>` já vem com um GUID único gerado para você. Se for criar variações,
> gere outro GUID para cada uma.

## Passo 3 — Carregar (sideload) no Excel

**Excel para Windows**
1. Coloque o `manifest.xml` numa pasta de rede compartilhada (ex.: `\\PC\Suplementos`).
2. No Excel: Arquivo → Opções → Centro de Confiabilidade → Configurações do Centro de Confiabilidade → **Catálogos de Suplementos Confiáveis**.
3. Cole o caminho da pasta de rede, marque "Mostrar no Menu" e clique em Adicionar catálogo. Feche e reabra o Excel.
4. Página Inicial → Suplementos → Suplementos Compartilhados → selecione "Assistente IA — Excel".

**Excel na web**
1. Inserir → Suplementos → Carregar Meu Suplemento → Procurar → selecione o `manifest.xml`.

**Excel para Mac**
1. Copie o `manifest.xml` para `~/Library/Containers/com.microsoft.Excel/Data/Documents/wef`
   (crie a pasta `wef` se não existir).
2. Reabra o Excel; o botão aparece na guia Página Inicial.

## Passo 4 — Usar

1. Na guia **Página Inicial**, clique em **Abrir Assistente**.
2. Abra **Chave da API**, cole sua chave `sk-...`, escolha o modelo e salve.
3. Selecione as células, escolha o modo, escreva a instrução e clique em **Executar na seleção**.

---

## Custo

A chave de API da OpenAI é cobrada **por uso (tokens)** e **não** vem incluída no ChatGPT Plus —
é uma conta/cobrança separada. O modelo `gpt-4o-mini` é o mais econômico para lote.
No modo "Por célula", cada célula é uma chamada à API; selecionar muitas células de uma vez
multiplica o custo e o tempo.

## Segurança (distribuição para a equipe)

A chave fica visível no navegador do Office (é uma aplicação web no cliente). Para **uso pessoal**,
tudo bem. Para distribuir aos ~37 usuários, o recomendável é não embutir a chave: criar um
**proxy** (um pequeno backend) que guarda a chave no servidor e repassa as requisições — assim
ninguém vê a chave e você controla custo/limites por usuário.
