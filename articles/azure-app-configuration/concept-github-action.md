---
title: Synchronizovat úložiště GitHub s konfigurací aplikace
description: Použijte akce GitHubu k automatické aktualizaci instance konfigurace aplikace při aktualizaci úložiště GitHub.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 602ccddf97938022df3c5903b573608558fe5d35
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585486"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>Synchronizovat úložiště GitHub s konfigurací aplikace

Týmy, které chtějí dál používat své stávající postupy správy zdrojového kódu, můžou použít akce GitHubu k automatické synchronizaci svého úložiště GitHub s úložištěm konfigurace aplikace. To vám umožní provádět změny v konfiguračních souborech, jako byste to obvykle měli, a přitom získat výhody konfigurace aplikace jako: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Centralizovaná konfigurace mimo váš kód <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Aktualizace konfigurace bez opětovného nasazení celé aplikace <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integrace se službami, jako jsou Azure App Service a funkce. 

[Pracovní postup](https://help.github.com/articles/about-github-actions#workflow) akcí GitHubu definuje automatizovaný proces v úložišti GitHub. Akce *synchronizace konfigurace aplikace Azure* aktivuje aktualizace instance konfigurace aplikace, když se provedou změny ve zdrojovém úložišti. Pomocí souboru YAML (. yml), který se nachází v `/.github/workflows/` cestě k úložišti, můžete definovat kroky a parametry. Aktualizace konfigurace můžete aktivovat při doručování, revizi nebo rozvětvení konfiguračních souborů aplikace stejně jako s kódem aplikace.

[Dokumentace k](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) GitHubu poskytuje podrobný pohled na pracovní postupy a akce GitHubu. 

## <a name="enable-github-actions-in-your-repository"></a>Povolení akcí GitHubu v úložišti
Pokud chcete začít používat tuto akci GitHubu, přejděte do úložiště a vyberte kartu **Akce** . klikněte na **nový pracovní postup**a pak **nastavte pracovní postup sami**. Nakonec na webu Marketplace vyhledejte "synchronizace konfigurace aplikací Azure".
> [!div class="mx-imgBorder"]
> ![Vyberte kartu akce.](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Vybrat akci synchronizace konfigurace aplikace](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Synchronizovat konfigurační soubory po vložení
Tato akce synchronizuje konfigurační soubory aplikace Azure při vložení změny do `appsettings.json`. Když vývojář nahraje změnu na `appsettings.json`, akce synchronizace konfigurace aplikace aktualizuje instanci konfigurace aplikace o nové hodnoty.

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

## <a name="use-a-dynamic-label-on-sync"></a>Při synchronizaci použít dynamický popisek
Předchozí akce aktualizuje instanci konfigurace aplikace vždy, když `appsettings.json` je aktualizace aktualizována. Tato akce vloží do každé synchronizace dynamický popisek, což zajistí, že každou synchronizaci je možné jednoznačně identifikovat a povolit mapování změn kódu na změny konfigurace.

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

## <a name="use-strict-sync"></a>Použít striktní synchronizaci
Pokud je povolen striktní režim, synchronizace zajistí, že instance konfigurace aplikace bude odpovídat konfiguračnímu souboru pro danou předponu a popisek přesně. Odstraní se páry klíč-hodnota se stejnou předponou a popiskem, které nejsou v konfiguračním souboru. 
 
Pokud není režim Strict povolený, bude synchronizace nastavovat jenom hodnoty klíč-hodnota z konfiguračního souboru. Neodstraní se žádné páry klíč-hodnota. 

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

## <a name="use-max-depth-to-limit-github-action"></a>Omezit akci GitHubu pomocí maximální hloubky
Výchozím chováním pro vnořené atributy JSON je sloučení celého objektu.  Následující kód JSON definuje tuto dvojici klíč-hodnota:

| Key | Hodnota |
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

| Key | Hodnota |
| --- | --- |
| Objekt: vnitřní | {"InnerKey": "InnerValue"} |

## <a name="understand-action-inputs"></a>Pochopení vstupů akcí
Vstupní parametry určují data, která akce používá během běhu.  Následující tabulka obsahuje vstupní parametry akceptované synchronizací konfigurace aplikace a očekávané hodnoty pro každý z nich.  Další informace o vstupech akcí pro akce GitHubu najdete v [dokumentaci k](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)GitHubu.

> [!Note]
> U ID vstupu se nerozlišují malá a velká písmena.


| Název vstupu | Povinné? | Hodnota |
|----|----|----|
| configurationFile | Ano | Relativní cesta ke konfiguračnímu souboru v úložišti  Jsou podporovány vzory glob a mohou obsahovat více souborů. |
| formát | Ano | Formát souboru konfiguračního souboru.  Platné formáty jsou: JSON, YAML, Properties. |
| připojovací řetězec | Ano | Připojovací řetězec pro instanci konfigurace aplikace Připojovací řetězec by měl být uložen jako tajný klíč v úložišti GitHub a v pracovním postupu by měl být použit pouze tajný název. |
| oddělování | Ano | Oddělovač použitý při sloučení konfiguračního souboru na páry klíč-hodnota.  Platné hodnoty jsou:. , ; : - _ __ / |
| směr | Ne | Předpona, která se má přidat na začátek klíčů |
| label | Ne | Popisek použitý při nastavování párů klíč-hodnota Je-li tento parametr zadán, je použit popisek s hodnotou null. |
| přísné | Ne | Logická hodnota, která určuje, zda je povolen striktní režim. Výchozí hodnota je False. |
| úrovní | Ne | Maximální hloubka pro sloučení konfiguračního souboru.  Hloubka musí být kladné číslo.  Výchozí hodnota nebude mít žádnou maximální hloubku. |
| tags | Ne | Určuje sadu značek pro páry klíč-hodnota.  Očekávaným formátem je dokument formulář objektu JSON následujícího obrazce: {[propertyName: String]: String;} Název každé vlastnosti – hodnota se zobrazí jako značka. |

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o akci GitHubu pro synchronizaci konfigurace aplikace a o tom, jak se dá použít k automatizaci aktualizací instance konfigurace aplikace. Další informace o tom, jak konfigurace aplikace Azure reaguje na změny v páru klíč-hodnota, najdete v dalším [článku](./concept-app-configuration-event.md).
