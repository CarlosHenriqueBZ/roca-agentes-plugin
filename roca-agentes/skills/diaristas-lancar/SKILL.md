---
name: diaristas-lancar
description: Lança no Mais Controle os pagamentos de diaristas da semana. Use quando ela pedir "lança os diaristas", "processa o extrato", "gera a planilha de importação" ou mandar o relatório PIX / OFX do banco que pagou os diaristas.
---

# Lançar diaristas no Mais Controle

## Arquivos: ela manda no chat, você cuida do resto

- **Não existe pasta de trabalho.** Use os arquivos que ela anexar ou arrastar na conversa (o caminho
  chega na mensagem). Nunca peça para ela salvar ou mover arquivo para alguma pasta.
- **Faltou arquivo? Peça no chat**, em uma frase, dizendo qual e de onde sai — ex.: "Me manda o
  relatório de PIX do banco que pagou (no internet banking: Movimentações PIX → Excel)".
- **Exportações do Mais Controle** (Pagamentos "Exportar Excel", Apontamento): antes de pedir, procure
  a mais recente na pasta Downloads dela (`Pagamentos-*.xlsx`, `Apontamento de Mão de Obra*.xlsx`) e
  confirme em uma linha: "Vou usar Pagamentos-2026-10-29.xlsx, de hoje — ok?". Só peça se não achar
  ou se for velha (mais de 2 dias).
- **Configuração** (apelidos de obra, diárias, conta do banco) fica sozinha em `Documentos\Roca\config`;
  **resultados** em `Documentos\Roca\saida\<data>`. Não passe `--config`/`--saida`.
- **Programa**: `roca.exe` na pasta `bin/` do plugin (duas pastas acima deste SKILL.md); em
  desenvolvimento, `python -m roca`. Sempre com os caminhos completos dos arquivos entre aspas.
- No fim, mande o **link do arquivo gerado** para ela abrir.

Transforma o extrato do banco que pagou os diaristas da semana em uma planilha no
**modelo de importação de Pagamentos do Mais Controle**, separando cada diária na obra certa.
Ela importa a planilha no Mais Controle e confere na tela de análise. **Você nunca grava nada
direto no Mais Controle.**

## Entradas

O que ela manda no chat:

1. **Extrato do banco que pagou a semana** (todos os diaristas saem do mesmo banco, mas o banco muda de semana para semana):
   - de preferência o **relatório "Movimentações PIX" em .xlsx** — traz a descrição do PIX
     (ex.: `05 DIARIAS GALPAO X`, `1 OBRA A / 4 OBRA B`), que diz quantas diárias e em qual obra;
   - ou o **.ofx** — não tem descrição; aí a obra sai do apontamento (item 2).
2. **Obrigatório na prática:** exportação de **Pagamentos já lançados** do mês — no Mais Controle,
   Financeiro > Pagamentos > botão "Exportar Excel" (ícone de download ao lado do filtro; não use
   "Relatórios", que sai em PDF). Evita lançar de novo o que ela ou outra pessoa já lançou à mão.
3. **Opcional, recomendado:** Apontamento de Mão de Obra (analítico) da semana, exportado do
   Mais Controle em Exportar > Diário de Obras. Serve para conferir se a pessoa trabalhou mesmo.

Pergunte qual banco foi usado na semana se não estiver claro pelo arquivo.

## Passo a passo

1. Se `DocumentosRocanfignfig.json` não existir, crie você mesmo: a **conta** de cada banco e a
   **categoria** das diárias saem da exportação de Pagamentos (colunas Conta Bancária e Categoria dos lançamentos de diária). Só confirme com ela.
2. Rode (veja "Arquivos" acima — caminhos completos dos arquivos que ela mandou):

   ```bash
   roca lancar --extrato "<arquivo>" --banco "<Banco>" --lancados "<Pagamentos-AAAA-MM-DD.xlsx>" --apontamento "<apontamento>.xlsx"
   ```

   Saem dois arquivos em `saida/`: `importacao_diaristas_*.xlsx` e `revisao_diaristas_*.md`.
3. **Apelido de obra desconhecido** (pendência "obra"): ela escreve apelidos no PIX
   ("GALPAO X", "SQS 999") que não são o nome da obra no Mais Controle.
   - Proponha a obra mais provável comparando com os nomes de obra que já estão em `obras.csv`
     e no apontamento.
   - **Confirme com ela (widget) antes de gravar.** Com o ok, grave
     com `roca obras --adicionar "APELIDO=OBRA"` e rode de novo. Cada apelido só é perguntado uma vez.
4. Leia o `revisao_*.md` e resuma para ela em poucas linhas:
   - ✅ quantos lançamentos prontos e o total;
   - ⚠️ alertas (diária fracionada, valor por diária fora da faixa, dias diferentes do apontamento,
     obra deduzida do apontamento) — um por linha, com nome e motivo;
   - ❌ o que ficou de fora e precisa de decisão;
   - 🔁 o que já estava lançado no Mais Controle (não vai na planilha);
   - ➖ contratos e outros pagamentos (não são diárias: ficam para a baixa normal).
5. Diga onde importar: **Financeiro > Pagamentos > ⋮ > Importar Lançamentos > Pagamentos**, enviar o
   `importacao_diaristas_*.xlsx` e conferir a etapa "Análise dos dados" antes de finalizar.

## Regras

- Não invente obra, quantidade ou valor. Na dúvida, deixe como pendência e pergunte.
- Um PIX dividido entre obras vira um lançamento por obra, com valor proporcional às diárias.
- Pagamentos com "CONTRATO" ou "MEDIÇÃO" são de empreiteiro: não entram na planilha de diaristas.
- Os arquivos têm nome, CPF e chave PIX de trabalhadores (LGPD): não copie esses dados para fora
  de Documentos\Roca e, ao terminar, sugira apagar de Downloads os extratos já processados.

## Como mostrar o resultado (widget)

**Faça você mesmo.** Nunca peça para ela procurar informação que está nos arquivos que ela mandou, em Downloads ou no
Mais Controle (nome de obra, valor, conta). Os nomes reais das obras vêm das exportações que ela já
baixa (Pagamentos, Apontamento) — passe-as com `--lancados` / `--apontamento`. Se não houver nenhuma
em Downloads e você tiver acesso ao navegador com o Mais Controle logado, leia a lista em
Obras > Minhas Obras (só leitura). Ela só **confirma** ou **escolhe**.

1. O comando grava `saida/lancar_<data>.json`. Leia o mais recente.
2. Leia o `widget.html` desta pasta (ao lado deste SKILL.md), troque `__DADOS__` pelo conteúdo do
   JSON (o objeto inteiro, sem aspas em volta) e mostre com a ferramenta de widget/visualização
   (`show_widget`). Não reescreva o HTML: use o modelo como está.
3. Se a ferramenta de widget não existir nesta conversa, resuma em texto curto e pergunte com
   respostas de "sim/não" ou de escolher entre opções numeradas.
4. Quando chegar a resposta do widget (mensagem começando com "Confirmo", "Obras", "Baixas"...):
   - obras confirmadas → grave com
     `roca obras --adicionar "APELIDO=OBRA" "APELIDO2=OBRA2"` e rode o comando de novo;
   - escolhas de valor/parcela → registre na resposta e siga; nada é gravado no Mais Controle por você.
5. Depois, diga em uma linha onde está o arquivo final (planilha de importação, conferência ou relatório).

## Depois de gerar a planilha (botões do widget)

Quando não houver obra pendente, o widget mostra o bloco **"Importar no Mais Controle"** com os passos e
os botões. Trate as respostas assim:

- **"Abre a planilha de importação: <caminho>"** → abra o arquivo no Excel dela
  (`cmd /c start "" "<caminho>"`) e diga em uma linha que abriu. Não peça para ela procurar o arquivo.
- **"Já importei a planilha de diaristas no Mais Controle."** →
  1. agradeça em uma linha e diga o total importado;
  2. liste os arquivos com dados de trabalhadores usados nesta rodada que estão em Downloads (relatório
     PIX, OFX, planilhas) e pergunte: "Apago esses arquivos de Downloads?". Só apague com o sim dela;
  3. lembre o próximo passo da semana, se houver (ex.: baixas de amanhã).
- O botão **"Abrir importação no Mais Controle"** abre direto Financeiro › Pagamentos › Importar
  Lançamentos; você não precisa fazer nada.
