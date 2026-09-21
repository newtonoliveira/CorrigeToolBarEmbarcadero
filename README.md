# corrigeToolBarIdeEmbarcadero

## O que o aplicativo faz

Aplicativo de console para Windows que remove a chave do registro:

```
HKEY_CURRENT_USER\Software\Embarcadero\BDS\<versao>\Toolbars
```

Essa chave costuma ficar corrompida/travada e causar problemas na toolbar do IDE Embarcadero (RAD Studio/Delphi), que reaproveita o mecanismo de toolbars do Internet Explorer. Ao remover a chave, o Windows a recria do zero na próxima vez que for necessária.

⚠️ **A exclusão é feita por conta e risco do usuário.** Faça um backup do registro antes de rodar o utilitário (regedit → Arquivo → Exportar).

## Escolha da versão instalada

O utilitário não assume mais uma versão fixa do RAD Studio/Delphi. Na inicialização ele varre `HKEY_CURRENT_USER\Software\Embarcadero\BDS` e lista apenas as subchaves de versão que realmente possuem `Toolbars`:

- **Uma única versão encontrada:** é usada automaticamente, sem perguntar nada ao usuário.
- **Duas ou mais versões encontradas:** é exibido um menu numerado, com o número da versão e o nome comercial correspondente, para o usuário escolher:

```
Foram encontradas 2 versoes do RAD Studio / Delphi com Toolbars no registro.
Escolha a versao para resetar o toolbar:
  1. 23.0 - RAD Studio 12 Athens
  2. 37.0 - RAD Studio 13 Florence
```

- **Nenhuma versão encontrada:** o programa avisa e encerra sem fazer nada.

Os nomes comerciais vêm de uma tabela interna (`NOMES_VERSOES`, no início do `.dpr`) que mapeia o número da chave BDS para o nome do produto (de `BDS 2005` até `RAD Studio 13 Florence`). Uma versão que não conste na tabela ainda é listada normalmente, só que com um nome genérico (`RAD Studio / Delphi versao X`) no lugar do nome comercial.

## Como funciona o código de confirmação

O código não é um segredo fixo nem precisa ser calculado de cabeça pelo usuário — ele é **gerado e mostrado na própria tela**, antes do prompt de digitação:

```
código = dia atual (2 dígitos, com zero à esquerda) + minuto atual (2 dígitos, com zero à esquerda)
```

Exemplo: no dia 8, às 14h35, o código é `0835`.

O objetivo não é autenticação de verdade — é obrigar quem está rodando o utilitário a **ler o aviso até encontrar o código**, em vez de apertar Enter sem prestar atenção antes de uma operação irreversível. Por isso o código aparece embutido em uma frase diferente a cada execução (a frase é sorteada aleatoriamente entre alguns modelos, na função `MostrarAvisoComCodigo`), obrigando a leitura em vez de deixar o código sempre no mesmo lugar previsível.

## Como compilar

Abra o arquivo `corrigeToolBarIdeEmbarcadero.dpr` como um projeto de aplicativo de console no RAD Studio, ou compile pela linha de comando:

```
dcc32 corrigeToolBarIdeEmbarcadero.dpr
```

Não são necessárias units externas além das que já estão no `uses` — todas fazem parte da RTL padrão do Delphi (`System.SysUtils`, `System.Classes`, `System.Win.Registry`, `Winapi.Windows`, `Winapi.ShlwApi`, `Winapi.TlHelp32`).

## Ajustes possíveis

- **`IDE_PROCESS_NAME`** (início do arquivo): define qual processo é considerado "IDE aberta" (`bds.exe`, padrão desde o Delphi 2007/BDS). Ajuste se usar outra versão/nome de executável.
- **`NOMES_VERSOES`** (início do arquivo): tabela que mapeia o número da chave BDS no registro (ex.: `23.0`) para o nome comercial (ex.: `RAD Studio 12 Athens`). Adicione uma linha aqui quando uma nova versão do RAD Studio for lançada.
- **`GetCodigoAtual`**: isola a fórmula do código, caso queira trocar a regra (por exemplo, somar os números em vez de concatená-los).
- **`Modelos`** (dentro de `MostrarAvisoComCodigo`): os textos de aviso com o código embutido. Adicione, remova ou reescreva livremente para variar ainda mais as posições.
