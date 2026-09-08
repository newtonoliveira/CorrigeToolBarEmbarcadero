# corrigeToolBarIdeEmbarcadero

## O que o aplicativo faz

Aplicativo de console para Windows que remove a chave do registro:

```
HKEY_CURRENT_USER\Software\Embarcadero\BDS\37.0\Toolbars
```

Essa chave costuma ficar corrompida/travada e causar problemas na toolbar do IDE Embarcadero (RAD Studio/Delphi), que reaproveita o mecanismo de toolbars do Internet Explorer. Ao remover a chave, o Windows a recria do zero na próxima vez que for necessária.

⚠️ **A exclusão é feita por conta e risco do usuário.** Faça um backup do registro antes de rodar o utilitário (regedit → Arquivo → Exportar).

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

Não são necessárias units externas além das que já estão no `uses` — todas fazem parte da RTL padrão do Delphi (`System.SysUtils`, `System.Win.Registry`, `Winapi.Windows`, `Winapi.ShlwApi`, `Winapi.TlHelp32`).

## Ajustes possíveis

- **`IDE_PROCESS_NAME`** (início do arquivo): define qual processo é considerado "IDE aberta" (`bds.exe`, padrão desde o Delphi 2007/BDS). Ajuste se usar outra versão/nome de executável.
- **`GetCodigoAtual`**: isola a fórmula do código, caso queira trocar a regra (por exemplo, somar os números em vez de concatená-los).
- **`Modelos`** (dentro de `MostrarAvisoComCodigo`): os textos de aviso com o código embutido. Adicione, remova ou reescreva livremente para variar ainda mais as posições.
