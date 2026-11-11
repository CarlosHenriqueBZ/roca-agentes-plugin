---
name: notas
description: Confere as notas fiscais (XML) emitidas contra o CNPJ com o que está lançado no Mais Controle e organiza os XMLs em pastas por mês e obra. Use quando ela pedir "confere as notas", "organiza os XMLs" ou mandar o .zip de XMLs do portal (toda quinta).
---

# Notas fiscais: conferir e organizar (quinta)

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

- **XMLs das notas**: o .zip baixado do portal (do jeito que vem) ou uma pasta com os .xml.
- **Pagamentos lançados**: Mais Controle > Financeiro > Pagamentos > botão "Exportar Excel".
  Exporte o **mês das notas e o mês seguinte** (nota de setembro costuma ser paga em outubro) e
  passe os dois arquivos.

## Passo a passo

1. Rode (veja "Arquivos" acima — caminhos completos dos arquivos que ela mandou):

   ```bash
   roca notas --xmls "<arquivo>.zip" --lancados "<Pagamentos-mes-da-nota.xlsx>" "<Pagamentos-mes-seguinte.xlsx>"
   ```

2. Resuma a partir do `saida/notas_*.xlsx` (já vem ordenado por prioridade):
   - **NÃO LANÇADA** — fornecedor sem nenhum lançamento no período: precisa lançar. Liste emitente,
     número, valor e obra sugerida.
   - **CONFERIR** — o fornecedor tem lançamentos, mas nenhum com o número da nota ou o mesmo valor:
     pode ser lançado com outro valor, outra data ou agrupado. Peça para ela olhar.
   - **LANÇADA** — achou pelo número do documento ou pelo valor/parcela.
3. **Obra**: vem do lançamento encontrado; se não houver, do texto da nota (apelido de
   `obras.csv` ou nome da obra). Notas sem obra ficam em `_SEM OBRA`: pergunte a obra das mais
   relevantes e, se for um apelido novo, grave com `roca obras --adicionar` e rode de novo.
4. Diga onde ficaram os XMLs organizados: `saida/notas_por_obra/AAAA-MM/<obra>/`. Os originais
   não são alterados.

## Regras

- Nunca apague os XMLs originais.
- O número "SOLICITAÇÃO" da nota é a solicitação de compra do Mais Controle: mostre quando ajudar
  a achar a obra.

## Como mostrar o resultado (widget)

**Faça você mesmo.** Nunca peça para ela procurar informação que está nos arquivos que ela mandou, em Downloads ou no
Mais Controle (nome de obra, valor, conta). Os nomes reais das obras vêm das exportações que ela já
baixa (Pagamentos, Apontamento) — passe-as com `--lancados` / `--apontamento`. Se não houver nenhuma
em Downloads e você tiver acesso ao navegador com o Mais Controle logado, leia a lista em
Obras > Minhas Obras (só leitura). Ela só **confirma** ou **escolhe**.

1. O comando grava `saida/notas_<data>.json`. Leia o mais recente.
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
