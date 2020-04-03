---
title: Synchronizace úložiště GitHub u konfigurace aplikace
description: Akce GitHubu se používají k automatické aktualizaci instance konfigurace aplikace při aktualizaci úložiště GitHub.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 602ccddf97938022df3c5903b573608558fe5d35
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585486"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>Synchronizace úložiště GitHub u konfigurace aplikace

Týmy, které chtějí nadále používat své stávající postupy správy zdrojového kódu, můžou pomocí akcí GitHubu automaticky synchronizovat své úložiště GitHub s úložištěm Konfigurace aplikací. To vám umožní provádět změny v konfiguračních souborech obvyklým způsobem, zatímco získáte výhody konfigurace aplikací, jako jsou: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Centralizovaná konfigurace mimo váš kód <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Aktualizace konfigurace bez opětovného nasazení celé aplikace <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integrace se službami, jako je služba Azure App Service a funkce. 

[Pracovní postup](https://help.github.com/articles/about-github-actions#workflow) Akce GitHubdefinuje automatizovaný proces v úložišti GitHub. Akce *synchronizace konfigurace aplikace Azure* aktivuje aktualizace instance Konfigurace aplikace při provádění změn ve zdrojovém úložišti. Používá soubor YAML (.yml), který `/.github/workflows/` se nachází v cestě vašeho úložiště k definování kroků a parametrů. Při odesílání, kontrole nebo větvení konfiguračních souborů aplikací můžete aktivovat aktualizace konfigurace stejně jako s kódem aplikace.

[Dokumentace](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) k GitHubu poskytuje podrobné zobrazení pracovních postupů a akcí GitHubu. 

## <a name="enable-github-actions-in-your-repository"></a>Povolení akcí GitHubu v úložišti
Chcete-li začít používat tuto akci GitHub, přejděte do svého úložiště a vyberte kartu **Akce.** Klikněte na **Nový pracovní postup**a nastavte pracovní postup **sami**. Nakonec vyhledejte na trhu "Azure App Configuration Sync."
> [!div class="mx-imgBorder"]
> ![Výběr karty Akce](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Výběr akce synchronizace konfigurace aplikace](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Synchronizace konfiguračních souborů po nabízení
Tato akce synchronizuje soubory Konfigurace aplikace Azure `appsettings.json`při zasunutí změny do . Když vývojář odešle `appsettings.json`změnu na , akce Synchronizace konfigurace aplikace aktualizuje instanci Konfigurace aplikace novými hodnotami.

První část tohoto pracovního postupu určuje, že akce `appsettings.json` se aktivuje *na* *push* obsahující *hlavní* větev. Druhá část obsahuje seznam úloh spuštěna po aktivaci akce. Akce rezervuje příslušné soubory a aktualizuje instanci Konfigurace aplikace pomocí připojovacího řetězce uloženého jako tajný klíč v úložišti.  Další informace o používání tajných kódů v GitHubu najdete v [článku GitHubu](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) o vytváření a používání šifrovaných tajných kódů.

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

## <a name="use-a-dynamic-label-on-sync"></a>Použití dynamického popisku při synchronizaci
Předchozí akce aktualizuje instanci `appsettings.json` konfigurace aplikace při každé aktualizaci. Tato akce vloží dynamický popisek na každou synchronizaci, zajištění, že každá synchronizace může být jednoznačně identifikována a umožňuje změny kódu mapovat na změny konfigurace.

První část tohoto pracovního postupu určuje, že akce `appsettings.json` se aktivuje *na* *push* obsahující *hlavní* větev. Druhá část spustí úlohu, která vytvoří jedinečný popisek pro aktualizaci konfigurace na základě hash potvrzení. Úloha pak aktualizuje instanci Konfigurace aplikace s novými hodnotami a jedinečnýpopisek pro tuto aktualizaci.

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

## <a name="use-strict-sync"></a>Použití přísné synchronizace
Pokud je povolen přísný režim, synchronizace zajišťuje, že instance Konfigurace aplikace přesně odpovídá konfiguračnímu souboru pro danou předponu a popisek. Dvojice mezi klíči a hodnota se stejnou předponou a popiskem, které nejsou v konfiguračním souboru, budou odstraněny. 
 
Pokud není povolen přísný režim, synchronizace nastaví pouze hodnoty klíče z konfiguračního souboru. Nebudou odstraněny žádné dvojice klíč-hodnota. 

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

## <a name="use-max-depth-to-limit-github-action"></a>Omezení akce GitHubu pomocí maximální hloubky
Výchozí chování pro vnořené atributy JSON je sloučí celý objekt.  JSON níže definuje tento pár klíč-hodnota:

| Klíč | Hodnota |
| --- | --- |
| Objekt:Vnitřní:InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Pokud je vnořený objekt určen jako hodnota posunutá do instance Configuration, můžete pomocí hodnoty *hloubky* zastavit sloučení ve stejné hloubce. 

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

Vzhledem k hloubce 2 výše uvedený příklad nyní vrátí následující dvojici klíč-hodnota:

| Klíč | Hodnota |
| --- | --- |
| Objekt:Vnitřní | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Principy vstupů akcí
Vstupní parametry určují data používaná akcí za běhu.  Následující tabulka obsahuje vstupní parametry přijaté synchronizací konfigurace aplikace a očekávané hodnoty pro každou z nich.  Další informace o vstupech akcí pro akce GitHubu najdete v [dokumentaci githubu](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs).

> [!Note]
> Vstupní ID jsou malá a velká písmena.


| Vstupní název | Povinné? | Hodnota |
|----|----|----|
| konfigurační soubor | Ano | Relativní cesta ke konfiguračnímu souboru v úložišti.  Glob vzory jsou podporovány a mohou obsahovat více souborů. |
| formát | Ano | Formát souboru konfiguračního souboru.  Platné formáty jsou: JSON, YAML, vlastnosti. |
| připojovací řetězec | Ano | Připojovací řetězec pro instanci Konfigurace aplikace. Připojovací řetězec by měl být uložen jako tajný klíč v úložišti GitHub a v pracovním postupu by měl být použit pouze název tajného klíče. |
| Oddělovač | Ano | Oddělovač používaný při sloučení konfiguračního souboru na dvojice klíč-hodnota.  Platné hodnoty jsou: . , ; : - _ __ / |
| Předponu | Ne | Předpona, která má být přidána na začátek klíčů. |
| label | Ne | Popisek použitý při nastavování párů klíč-hodnota. Pokud není zadán, null popisek se používá. |
| přísné | Ne | Logická hodnota, která určuje, zda je povolen přísný režim. Výchozí hodnota je False. |
| Hloubka | Ne | Maximální hloubka pro sloučení konfiguračního souboru.  Hloubka musí být kladné číslo.  Výchozí hodnota nebude mít maximální hloubku. |
| tags | Ne | Určuje značku nastavenou na páry klíč-hodnota.  Očekávaný formát je stringified forma Objekt JSON následujícího tvaru: { [propertyName: string]: string; } Každá hodnota názvu vlastnosti se stane značkou. |

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o akci GitHub synchronizace konfigurace aplikace a o tom, jak ji lze použít k automatizaci aktualizací instance konfigurace aplikace. Chcete-li zjistit, jak Azure App Configuration reaguje na změny v párech klíč hodnota, pokračujte v dalším [článku](./concept-app-configuration-event.md).
