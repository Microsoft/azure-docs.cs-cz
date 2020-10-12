---
title: Synchronizovat úložiště GitHub s konfigurací aplikace
description: Použijte akce GitHubu k automatické aktualizaci instance konfigurace aplikace při aktualizaci úložiště GitHub.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 66d0e32e7dfdd5ab2abee5108ac8ce54c5222747
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87371817"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>Synchronizovat úložiště GitHub s konfigurací aplikace

Týmy, které chtějí dál používat své stávající postupy správy zdrojového kódu, můžou použít akce GitHubu k automatické synchronizaci svého úložiště GitHub s úložištěm konfigurace aplikace. To vám umožní provádět změny v konfiguračních souborech, jako byste to obvykle měli, a přitom získat výhody konfigurace aplikace jako: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Centralizovaná konfigurace mimo váš kód <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Aktualizace konfigurace bez opětovného nasazení celé aplikace <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integrace se službami, jako jsou Azure App Service a funkce. 

[Pracovní postup](https://help.github.com/articles/about-github-actions#workflow) akcí GitHubu definuje automatizovaný proces v úložišti GitHub. Akce *synchronizace konfigurace aplikace Azure* aktivuje aktualizace instance konfigurace aplikace, když se provedou změny ve zdrojovém úložišti. Pomocí souboru YAML (. yml), který se nachází v `/.github/workflows/` cestě k úložišti, můžete definovat kroky a parametry. Aktualizace konfigurace můžete aktivovat při doručování, revizi nebo rozvětvení konfiguračních souborů aplikace stejně jako s kódem aplikace.

[Dokumentace k](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) GitHubu poskytuje podrobný pohled na pracovní postupy a akce GitHubu. 

## <a name="enable-github-actions-in-your-repository"></a>Povolení akcí GitHubu v úložišti
Tuto akci GitHubu můžete začít používat tak, že přejdete do svého úložiště a vyberete kartu **Akce** . Vyberte **nový pracovní postup**a pak **nastavte pracovní postup sami**. Nakonec na webu Marketplace vyhledejte "synchronizace konfigurace aplikací Azure".
> [!div class="mx-imgBorder"]
> ![Vyberte kartu akce.](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Vybrat akci synchronizace konfigurace aplikace](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Synchronizovat konfigurační soubory po vložení
Tato akce synchronizuje konfigurační soubory aplikace Azure při vložení změny do `appsettings.json` . Když vývojář nahraje změnu na `appsettings.json` , akce synchronizace konfigurace aplikace aktualizuje instanci konfigurace aplikace o nové hodnoty.

První část tohoto pracovního postupu určuje, že akce se spustí *u* *nabízených oznámení* , která obsahují `appsettings.json` *hlavní* větev. Druhá část uvádí úlohy, které se spustí, jakmile se spustí akce. Tato akce rezervuje příslušné soubory a aktualizuje instanci konfigurace aplikace pomocí připojovacího řetězce uloženého jako tajného klíče v úložišti.  Další informace o používání tajných kódů v GitHubu najdete v [článku GitHubu](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) o vytváření a používání šifrovaných tajných klíčů.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-strict-sync"></a>Použít striktní synchronizaci
Ve výchozím nastavení akce GitHub nepovoluje striktní režim, což znamená, že synchronizace přidá do instance aplikace pouze hodnoty klíče z konfiguračního souboru (žádné páry klíč-hodnota nebudou odstraněny). Povolení striktního režimu znamená, že páry klíč-hodnota, které nejsou v konfiguračním souboru, se odstraní z instance konfigurace aplikace tak, aby odpovídaly konfiguračnímu souboru. Pokud synchronizujete z více zdrojů nebo pomocí Azure Key Vault s konfigurací aplikace, budete chtít použít jiné předpony nebo popisky s přísnou synchronizací, aby nedocházelo k vymazání konfiguračních nastavení z jiných souborů (viz ukázky níže). 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```
## <a name="sync-multiple-files-in-one-action"></a>Synchronizace více souborů v jedné akci 

Pokud je vaše konfigurace ve více souborech, můžete použít následující vzor, který spustí synchronizaci při změně některého souboru. Tento model používá knihovnu glob https://www.npmjs.com/package/glob . Všimněte si, že pokud název konfiguračního souboru obsahuje čárku, můžete použít zpětné lomítko k označení čárky. 

```json
on:
  push:
    branches:
      - 'master'
    paths:
      - 'appsettings.json'
      - 'appsettings2.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: '{appsettings.json,appsettings2.json}'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
```

## <a name="sync-by-prefix-or-label"></a>Synchronizovat podle předpony nebo popisku
Zadáním předpon nebo popisků v akci synchronizace se synchronizuje pouze tato konkrétní sada. To je důležité pro použití striktní synchronizace s více soubory. V závislosti na tom, jak je konfigurace nastavená, může být k jednotlivým souborům přidružená buď předpona, nebo popisek, přičemž každou předponu nebo popisek můžete synchronizovat samostatně, aby se nic nepřepsalo. Předpony se obvykle používají pro různé aplikace nebo služby a popisky se používají pro různá prostředí. 

Synchronizace podle předpony: 

```json
on:
  push:
    branches:
      - 'master'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          prefix: 'Prefix::'
```

Synchronizovat podle popisku: 

```json
on:
  push:
    branches:
      - 'master'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          label: 'Label'

```

## <a name="use-a-dynamic-label-on-sync"></a>Při synchronizaci použít dynamický popisek
Následující akce vloží dynamický popisek pro každou synchronizaci, což zajistí, že každou synchronizaci lze jednoznačně identifikovat a povolit mapování změn kódu na změny konfigurace.

První část tohoto pracovního postupu určuje, že akce se spustí *u* *nabízených oznámení* , která obsahují `appsettings.json` *hlavní* větev. Druhá část spustí úlohu, která vytvoří jedinečnou jmenovku pro aktualizaci konfigurace na základě hodnoty hash potvrzení. Úloha pak aktualizuje instanci konfigurace aplikace o nové hodnoty a jedinečnou jmenovku pro tuto aktualizaci.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-azure-key-vault-with-github-action"></a>Použití Azure Key Vault s akcí GitHubu
Vývojáři, kteří používají Azure Key Vault s AppConfiguration, by měli používat dva samostatné soubory, obvykle appsettings.jsa secretreferences.jsna. secretreferences.jsna bude obsahovat adresu URL tajného klíče trezoru klíčů.

{"mySecret": "{ \" URI \" :" \" https://myKeyVault.vault.azure.net/secrets/mySecret } "}

Akce GitHubu se pak dá nakonfigurovat tak, aby se na appsettings.jsna, a po secretreferences.jsnestriktní synchronizaci na systému nakonfigurovala striktní synchronizace. Následující ukázka spustí synchronizaci, je-li soubor aktualizován:

```json
on:
  push:
    branches:
      - 'master'
    paths:
      - 'appsettings.json'
      - 'secretreferences.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          strict: true
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'secretreferences.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          contentType: 'application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8'

```

## <a name="use-max-depth-to-limit-github-action"></a>Omezit akci GitHubu pomocí maximální hloubky
Výchozím chováním pro vnořené atributy JSON je sloučení celého objektu.  Následující kód JSON definuje tuto dvojici klíč-hodnota:

| Klíč | Hodnota |
| --- | --- |
| Objekt: vnitřní: InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Pokud má vnořený objekt hodnotu vloženou do instance konfigurace, můžete použít hodnotu *hloubky* k zastavení sloučení v příslušné hloubce. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

V případě, že výše uvedená hloubka je 2, vrátí výše uvedený příklad následující pár klíč-hodnota:

| Klíč | Hodnota |
| --- | --- |
| Objekt: vnitřní | {"InnerKey": "InnerValue"} |

## <a name="understand-action-inputs"></a>Pochopení vstupů akcí
Vstupní parametry určují data, která akce používá během běhu.  Následující tabulka obsahuje vstupní parametry akceptované synchronizací konfigurace aplikace a očekávané hodnoty pro každý z nich.  Další informace o vstupech akcí pro akce GitHubu najdete v [dokumentaci k](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)GitHubu.

> [!Note]
> U ID vstupu se nerozlišují malá a velká písmena.


| Název vstupu | Povinné? | Hodnota |
|----|----|----|
| configurationFile | Yes | Relativní cesta ke konfiguračnímu souboru v úložišti  Jsou podporovány vzory glob a mohou obsahovat více souborů. |
| formát | Yes | Formát souboru konfiguračního souboru.  Platné formáty jsou: JSON, YAML, Properties. |
| připojovací řetězec | Yes | Připojovací řetězec pro instanci konfigurace aplikace Připojovací řetězec by měl být uložen jako tajný klíč v úložišti GitHub a v pracovním postupu by měl být použit pouze tajný název. |
| oddělování | Yes | Oddělovač použitý při sloučení konfiguračního souboru na páry klíč-hodnota.  Platné hodnoty jsou:. , ; : - _ __ / |
| směr | No | Předpona, která se má přidat na začátek klíčů |
| label | No | Popisek použitý při nastavování párů klíč-hodnota Je-li tento parametr zadán, je použit popisek s hodnotou null. |
| přísné | No | Logická hodnota, která určuje, zda je povolen striktní režim. Výchozí hodnota je False. |
| úrovní | No | Maximální hloubka pro sloučení konfiguračního souboru.  Hloubka musí být kladné číslo.  Výchozí hodnota nebude mít žádnou maximální hloubku. |
| tags | No | Určuje sadu značek pro páry klíč-hodnota.  Očekávaným formátem je dokument formulář objektu JSON následujícího obrazce: {[propertyName: String]: String;} Název každé vlastnosti – hodnota se zobrazí jako značka. |

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o akci GitHubu pro synchronizaci konfigurace aplikace a o tom, jak se dá použít k automatizaci aktualizací instance konfigurace aplikace. Další informace o tom, jak konfigurace aplikace Azure reaguje na změny v páru klíč-hodnota, najdete v dalším [článku](./concept-app-configuration-event.md).
