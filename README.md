# roca-agentes

Plugin do Claude com as rotinas financeiras no Mais Controle: planilha e lançamento de diaristas, conferência de notas e baixas.

## Instalação

Pré-requisitos: Claude Desktop (aba **Code**) ou Claude Code instalado, e acesso a este repositório no GitHub (o git da máquina precisa estar logado na conta convidada).

No terminal:

```bash
claude plugin marketplace add CarlosHenriqueBZ/roca-agentes-plugin
claude plugin install roca-agentes@roca
```

Ou dentro de uma sessão do Claude:

```
/plugin marketplace add CarlosHenriqueBZ/roca-agentes-plugin
/plugin install roca-agentes@roca
```

Depois reinicie o Claude Desktop. As rotinas aparecem na aba **Code**.

## Atualizar

```bash
claude plugin marketplace update roca
```

## Configuração

A pasta `roca-agentes/config-exemplo` mostra o formato dos arquivos de configuração (contas, obras e diárias) com dados fictícios. Preencha com os dados reais apenas na máquina de uso; não envie esses arquivos para este repositório.
