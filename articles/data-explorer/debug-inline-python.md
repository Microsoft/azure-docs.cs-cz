---
title: Ladění dotazovacího jazyka Kusto v členě Pythonu pomocí kódu VS – Průzkumník dat Azure
description: Naučte se ladit Kusto dotazovací jazyk (KQL) vložkou Python pomocí VS Code.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 96bd66f96b04bd7032d976ba9ebbbeb60c8415e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444474"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Ladění dotazovacího jazyka Kusto v souladu s Pythonem pomocí kódu VS

Azure Data Explorer podporuje spuštění kódu Pythonu vloženého do dotazovacího jazyka Kusto pomocí [pluginu python().](/azure/kusto/query/pythonplugin) Modul runtime pluginu je hostován v izolovaném a zabezpečeném prostředí Pythonu. Python() plugin schopnost rozšiřuje Kusto dotazovací jazyk nativní funkce s obrovským archiv balíčků OSS Python. Toto rozšíření umožňuje spouštět pokročilé algoritmy, jako je například strojové učení, umělá inteligence, statistické a časové řady jako součást dotazu.

Nástroje pro dotazovací jazyk Kusto nejsou vhodné pro vývoj a ladění algoritmů Pythonu. Proto rozvíjet algoritmus na své oblíbené vývojové prostředí integrované Pythonu, jako je Jupyter, PyCharm, VS nebo VS kód. Po dokončení algoritmu zkopírujte a vložte do KQL. Pro zlepšení a zefektivnění tohoto pracovního postupu podporuje Azure Data Explorer integraci mezi klienty Kusto Explorer nebo Web UI a Kód VS pro vytváření a ladění kódu KQL v pythonu. 

> [!NOTE]
> Tento pracovní postup lze použít pouze k ladění relativně malé vstupní tabulky (až několik MB). Proto může být nutné omezit vstup pro ladění.  Pokud potřebujete zpracovat velkou tabulku, omezte `| take` `| sample`ji `where rand() < 0.x`pro ladění pomocí , , nebo .

## <a name="prerequisites"></a>Požadavky

1. Nainstalujte [pythona Anaconda distribuce](https://www.anaconda.com/distribution/#download-section). V **části Upřesnit možnosti**vyberte **přidat anakondu do proměnné prostředí PATH**.
2. Instalace [kódu sady Visual Studio](https://code.visualstudio.com/Download)
3. Nainstalujte [rozšíření Pythonu pro visual studio kód](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

## <a name="run-your-query-in-your-client-application"></a>Spuštění dotazu v klientské aplikaci

1. V klientské aplikaci předponujedotaz obsahující vsazený Python s`set query_python_debug;`
1. Spusťte dotaz.
    * Kusto Explorer: VS Code se automaticky spustí se skriptem *debug_python.py.*
    * Webové uživatelské uzliNa Kusto: 
        1. Stáhněte a uložte *soubor debug_python.py*, *df.txt*a *kargs.txt*. V okně vyberte **Povolit**. **Uložit** soubory do vybraného adresáře. 

            ![Webové uživatelské rozhraní stáhne vložkové soubory pythonu](media/debug-inline-python/webui-inline-python.png)

        1. Klikněte pravým tlačítkem myši *na soubor debug_python.py* a otevřete jej pomocí kódu VS. 
        Skript *debug_python.py* obsahuje vložený kód Pythonu z dotazu KQL, který je předponou kódem šablony pro inicializaci vstupního datového rámce z *souboru df.txt* a slovníku parametrů z *souboru kargs.txt*.    
            
1. V kódu VS spusťte ladicí program kódu VS: **Ladění** > **ladění start ování (F5)**, vyberte konfiguraci **Pythonu.** Ladicí program se spustí a automaticky zarážka ladit vložených kód.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Jak funguje vslaná ladění Pythonu v kódu VS?

1. Dotaz je analyzován a proveden na serveru, `| evaluate python()` dokud není dosaženo požadované klauzule.
1. Izolovaného prostoru Pythonu je vyvolána, ale místo spuštění kódu serializuje vstupní tabulku, slovník parametrů a kód a odešle je zpět klientovi.
1. Tyto tři objekty jsou uloženy ve třech souborech: *df.txt*, *kargs.txt*a *debug_python.py* ve vybraném adresáři (Webové uživatelské rozhraní) nebo v adresáři %TEMP% klienta (Kusto Explorer).
1. VS kód je spuštěn, předinstalovaný se souborem *debug_python.py,* který obsahuje předponový kód pro inicializaci souborů DF a kargs z příslušných souborů, následovaný skriptem Pythonu vloženým do dotazu KQL.

## <a name="query-example"></a>Příklad dotazu

1. Spusťte v klientské aplikaci následující dotaz KQL:

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Viz výsledná tabulka:

    | x  | x4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Spusťte stejný dotaz KQL `set query_python_debug;`v klientské aplikaci pomocí :

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS Code je spuštěn:

    ![spuštění vs kódu](media/debug-inline-python/launch-vs-code.png)

1. VS Kód odchybovat a vytiskne 'výsledek' datový rámec v konzole ladění:

    ![Ladění kódu VS](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Mezi bitovou kopii izolovaného prostoru Pythonu a místní instalací mohou být rozdíly. [Zkontrolujte obrázek izolovaného prostoru pro konkrétní balíčky dotazem plugin](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl).
