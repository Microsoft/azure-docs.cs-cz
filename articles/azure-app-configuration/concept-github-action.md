---
title: Použití akcí GitHubu s synchronizací konfigurace aplikací Azure
description: Použijte akce GitHubu, pokud chcete aktivovat aktualizaci instance konfigurace aplikace, když se definované akce provádějí v úložišti GitHubu.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 9d60f1885a85fd7d45090f1cb4905a3d95d9d1d6
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523709"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Synchronizace instance App Configuration s využitím GitHub Actions
Konfigurace aplikace Azure používá akce GitHubu k aktivaci aktualizací instance konfigurace aplikace na základě akcí prováděných v úložišti GitHubu. Pracovní postupy na GitHubu aktivují aktualizace konfigurace a umožňují integraci těchto aktualizací do stejného pracovního postupu, který se používá k aktualizaci kódu aplikace.

[Pracovní postup](https://help.github.com/articles/about-github-actions#workflow) akcí GitHubu definuje automatizovaný proces v úložišti GitHub. Tento proces oznamuje GitHubu, jak sestavit a nasadit váš projekt GitHubu. Konfigurace aplikace Azure poskytuje akci *synchronizace konfigurace aplikace Azure* , která umožňuje aktualizace instance konfigurace aplikace při provedení změn ve zdrojovém úložišti. 

Soubor YAML (. yml), který se nachází v `/.github/workflows/` cestě k úložišti, definuje váš pracovní postup. Tato definice obsahuje kroky a parametry pracovního postupu.

Události GitHubu, například nabízené oznámení do úložiště, můžou aktivovat pracovní postup akce GitHubu.  Akce *synchronizace konfigurace aplikace Azure* umožňuje aktivovat aktualizaci instance konfigurace aplikace, když dojde k zadané akci GitHubu. Aktualizace konfigurace můžete aktivovat při doručování, revizi nebo rozvětvení konfiguračních souborů aplikace stejně jako s kódem aplikace.

[Dokumentace k](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) GitHubu poskytuje podrobný pohled na pracovní postupy a akce GitHubu. 

## <a name="enable-github-actions-in-your-repository"></a>Povolení akcí GitHubu v úložišti
Pokud chcete začít používat tuto akci GitHubu, přejděte do úložiště a vyberte kartu **Akce** . klikněte na **nový pracovní postup**a pak **nastavte pracovní postup sami**. Nakonec na webu Marketplace vyhledejte "synchronizace konfigurace aplikací Azure".
> [!div class="mx-imgBorder"]
> ![vyberte kartu akce](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![vybrat akci synchronizace konfigurace aplikace](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Synchronizovat konfigurační soubory po vložení
Tato akce synchronizuje konfigurační soubory aplikace Azure při vložení změny do `appsettings.json`. Když vývojář nahraje změnu `appsettings.json`, akce synchronizace konfigurace aplikace aktualizuje instanci konfigurace aplikace o nové hodnoty.

První část tohoto pracovního postupu určuje, že akce se spustí *na* vyžádání *push* obsahující `appsettings.json` do *hlavní* větve. Druhá část uvádí úlohy, které se spustí, jakmile se spustí akce. Tato akce rezervuje příslušné soubory a aktualizuje instanci konfigurace aplikace pomocí připojovacího řetězce uloženého jako tajného klíče v úložišti.  Další informace o používání tajných kódů v GitHubu najdete v [článku GitHubu](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) o vytváření a používání šifrovaných tajných klíčů.

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
Předchozí akce aktualizuje instanci konfigurace aplikace vždy, když se aktualizuje `appsettings.json`. Tato akce vloží do každé synchronizace dynamický popisek, což zajistí, že každou synchronizaci je možné jednoznačně identifikovat a povolit mapování změn kódu na změny konfigurace.

První část tohoto pracovního postupu určuje, že akce se spustí *na* vyžádání *push* obsahující `appsettings.json` do *hlavní* větve. Druhá část spustí úlohu, která vytvoří jedinečnou jmenovku pro aktualizaci konfigurace na základě hodnoty hash potvrzení. Úloha pak aktualizuje instanci konfigurace aplikace o nové hodnoty a jedinečnou jmenovku pro tuto aktualizaci.

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


| Název vstupu | Požadováno? | Hodnota |
|----|----|----|
| configurationFile | Ano | Relativní cesta ke konfiguračnímu souboru v úložišti  Jsou podporovány vzory glob a mohou obsahovat více souborů. |
| format | Ano | Formát souboru konfiguračního souboru.  Platné formáty jsou: JSON, YAML, Properties. |
| connectionString | Ano | Připojovací řetězec pro instanci konfigurace aplikace Připojovací řetězec by měl být uložen jako tajný klíč v úložišti GitHub a v pracovním postupu by měl být použit pouze tajný název. |
| oddělování | Ano | Oddělovač použitý při sloučení konfiguračního souboru na páry klíč-hodnota.  Platné hodnoty jsou:. , ; : - _ __ / |
| prefix | Ne | Předpona, která se má přidat na začátek klíčů |
| label | Ne | Popisek použitý při nastavování párů klíč-hodnota Je-li tento parametr zadán, je použit popisek s hodnotou null. |
| zásadní | Ne | Logická hodnota, která určuje, zda je povolen striktní režim. Výchozí hodnota je false. |
| úrovní | Ne | Maximální hloubka pro sloučení konfiguračního souboru.  Hloubka musí být kladné číslo.  Výchozí hodnota nebude mít žádnou maximální hloubku. |
| značek | Ne | Určuje sadu značek pro páry klíč-hodnota.  Očekávaným formátem je dokument formulář objektu JSON následujícího obrazce: {[propertyName: String]: String;} Název každé vlastnosti – hodnota se zobrazí jako značka. |

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o akci GitHubu pro synchronizaci konfigurace aplikace a o tom, jak se dá použít k automatizaci aktualizací instance konfigurace aplikace. Další informace o tom, jak konfigurace aplikace Azure reaguje na změny v páru klíč-hodnota, najdete v dalším [článku](./concept-app-configuration-event.md).
