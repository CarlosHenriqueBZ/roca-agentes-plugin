---
name: baixas
description: Cruza o extrato do banco (OFX ou relatório PIX) com as parcelas do Mais Controle e diz o que já tem baixa, o que precisa de baixa e o que nem foi lançado. Use quando ela pedir "faz as baixas", "o que falta dar baixa" ou mandar o extrato do dia anterior.
---

# Baixas do dia (toda manhã)

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

## Entradas

- **Extrato do dia anterior** de cada banco que teve pagamento: .ofx ou relatório PIX (.xlsx).
  Um comando por banco (`--banco` = nome do banco; a conta dele fica em `Documentos\Roca\config\config.json` — se faltar, descubra na exportação de Pagamentos, coluna Conta Bancária, e grave).
- **Pagamentos lançados**: Mais Controle > Financeiro > Pagamentos > "Exportar Excel" (mês atual;
  no começo do mês, passe também o anterior).

## Passo a passo

1. Rode (veja "Arquivos" acima — caminhos completos dos arquivos que ela mandou):

   ```bash
   roca baixas --extrato "<extrato>.ofx" --banco "<Banco>" --lancados "<Pagamentos-AAAA-MM-DD.xlsx>"
   ```

2. Resuma a partir do `saida/baixas_*.xlsx` (ordenado por prioridade):
   - **DAR BAIXA** — parcela em aberto do mesmo favorecido e valor: diga fornecedor, vencimento,
     descrição e obra para ela dar baixa no Mais Controle.
   - **VÁRIAS PARCELAS** — o favorecido tem mais de uma parcela em aberto com esse valor: ela escolhe.
   - **SEM LANÇAMENTO** — nada desse favorecido: precisa lançar. Se for diária, é o fluxo do
     `/diaristas-lancar`. As parcelas listadas aqui são de outros favorecidos com o mesmo valor
     (só pista — não dar baixa nelas sem ela confirmar).
   - **JÁ BAIXADO** — nada a fazer.

## Regras

- Só casa pagamento com parcela do **mesmo favorecido** (CPF/CNPJ ou nome). Valor igual sozinho não basta.
- Pagamento dividido entre obras aparece no Mais Controle como várias linhas ("106.1", "106.2"):
  o programa já soma.
- Você não dá baixa no sistema: a baixa é ela que faz (ou pela importação de OFX do próprio Mais Controle).

## Como mostrar o resultado (widget)

**Faça você mesmo.** Nunca peça para ela procurar informação que está nos arquivos que ela mandou, em Downloads ou no
Mais Controle (nome de obra, valor, conta). Os nomes reais das obras vêm das exportações que ela já
baixa (Pagamentos, Apontamento) — passe-as com `--lancados` / `--apontamento`. Se não houver nenhuma
em Downloads e você tiver acesso ao navegador com o Mais Controle logado, leia a lista em
Obras > Minhas Obras (só leitura). Ela só **confirma** ou **escolhe**.

1. O comando grava `saida/baixas_<data>.json`. Leia o mais recente.
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
