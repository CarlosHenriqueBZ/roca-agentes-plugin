---
name: diaristas-planilha
description: Confere a planilha de pagamento dos diaristas que ela monta (BSB, GYN... em PDF ou Excel), recalcula cada linha e gera o texto da descrição do PIX de cada pessoa. Use na sexta, antes de pagar, quando ela mandar as planilhas da semana ou pedir "confere a planilha dos diaristas" / "gera os textos do PIX".
---

# Conferir a planilha de pagamento e gerar o texto do PIX (sexta)

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

A fonte dos dias e das obras é **a planilha que ela monta** (uma por região: BSB, GYN...). O
diário de obra do Mais Controle não bate com o que ela paga, então não use o apontamento para
calcular pagamento. O agente:

1. recalcula cada linha com as regras dela e aponta onde a conta não fecha;
2. escreve a **descrição do PIX** de cada pessoa ("05 DIARIAS BYD GOIANIA", "01 DUILIO / 03 SQS 313").
   Ela cola esse texto no PIX — é o que faz o `/diaristas-lancar` de segunda achar a obra sozinho.

## Regras da planilha dela

- Cada dia tem a obra: `BYD GOIANIA` = diária cheia; `1/2 BYD GOIANIA` = meia diária.
- `+2HE` = 2 horas extras; **hora extra = diária ÷ 8**.
- Valor escrito no dia (`BYD GOIANIA 260,00`) = esse dia pago por esse valor, no lugar da diária.
- Coluna "Valor de Passagem" (BSB) soma ao total.
- Linha com total R$ 0,00 e sem dias = não trabalhou na semana (ignorar).

## Passo a passo

1. Rode (veja "Arquivos" acima — caminhos completos dos arquivos que ela mandou):

   ```bash
   roca planilha --planilha "<PLANILHA BSB.pdf>" "<PLANILHA GYN.pdf>" --lancados "<Pagamentos-AAAA-MM-DD.xlsx>"
   ```

   Aceita PDF ou o Excel original (melhor: Excel).
2. **Confira as datas** do cabeçalho da planilha: se forem de uma semana já paga, avise antes de qualquer coisa.
3. Resuma a partir do `saida/conferencia_pagamento_*.xlsx`:
   - total por planilha e geral;
   - **conta não fecha**: nome, total da planilha × calculado e a provável causa (hora extra não
     marcada, dia a mais/a menos, valor de dia diferente). Quem decide o valor é ela;
   - **TOTAL DIÁRIAS diferente dos dias marcados**;
   - **obra sem apelido no obras.csv**: não pergunte o nome da obra
     — o widget já traz a obra sugerida; com a confirmação, grave com `roca obras --adicionar`.
4. Entregue a lista **nome → descrição do PIX** (coluna "DESCRIÇÃO DO PIX") para ela usar ao pagar.

## Regras

- Não altere valores da planilha dela: só aponte divergências.
- Nome, chave PIX e valores de trabalhadores ficam só em Documentos\Roca (LGPD).
- Alternativa (só se ela pedir): `roca planilha --apontamento <arquivo>` monta pelo Apontamento de
  Mão de Obra do Mais Controle — hoje não bate com o que ela paga.

## Como mostrar o resultado (widget)

**Faça você mesmo.** Nunca peça para ela procurar informação que está nos arquivos que ela mandou, em Downloads ou no
Mais Controle (nome de obra, valor, conta). Os nomes reais das obras vêm das exportações que ela já
baixa (Pagamentos, Apontamento) — passe-as com `--lancados` / `--apontamento`. Se não houver nenhuma
em Downloads e você tiver acesso ao navegador com o Mais Controle logado, leia a lista em
Obras > Minhas Obras (só leitura). Ela só **confirma** ou **escolhe**.

1. O comando grava `saida/planilha_<data>.json`. Leia o mais recente.
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
