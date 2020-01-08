---
title: Ladění Kusto dotazovacího jazyka vloženého Pythonu pomocí VS Code-Azure Průzkumník dat
description: Naučte se ladit vložené Pythony Kusto Query Language (KQL) pomocí VS Code.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 96bd66f96b04bd7032d976ba9ebbbeb60c8415e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444474"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Ladění Kusto dotazovacího jazyka vložené Pythonu pomocí VS Code

Azure Průzkumník dat podporuje spouštění kódu Pythonu vloženého v dotazovacím jazyce Kusto pomocí [modulu plug-in Pythonu ()](/azure/kusto/query/pythonplugin). Běhový modul plug-in je hostovaný v izolovaném prostoru, izolovaném a zabezpečeném prostředí Pythonu. Schopnosti modulu plug-in Pythonu () rozšiřují nativní funkce jazyka dotazů Kusto s obrovským archivem balíčků OSS Python. Toto rozšíření umožňuje spouštět v rámci dotazu pokročilé algoritmy, jako je Machine Learning, umělá logika, statistická a časová řada.

Nástroje dotazovacího jazyka Kusto nejsou vhodné pro vývoj a ladění algoritmů Pythonu. Proto si vyvinete algoritmus ve svém oblíbeném vývojovém prostředí integrovaném v Pythonu, jako je Jupyter, PyCharm, VS nebo VS Code. Po dokončení algoritmu zkopírujte a vložte do KQL. Pro zlepšení a zjednodušení tohoto pracovního postupu Azure Průzkumník dat podporuje integraci mezi Kusto Explorerem nebo webovými klienty uživatelského rozhraní a VS Code pro vytváření a ladění vloženého kódu Pythonu KQL. 

> [!NOTE]
> Tento pracovní postup se dá použít jenom k ladění relativně malých vstupních tabulek (až pár MB). Proto možná budete muset omezit vstup pro ladění.  Pokud potřebujete zpracovat velkou tabulku, omezte ji pro ladění pomocí `| take`, `| sample`nebo `where rand() < 0.x`.

## <a name="prerequisites"></a>Požadavky

1. Instalace [distribuce Python Anaconda](https://www.anaconda.com/distribution/#download-section) V okně **Upřesnit možnosti**vyberte **Přidat Anaconda do proměnné prostředí moje cesta**.
2. Instalace [Visual Studio Code](https://code.visualstudio.com/Download)
3. Nainstalovat [rozšíření Python pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

## <a name="run-your-query-in-your-client-application"></a>Spuštění dotazu v klientské aplikaci

1. V klientské aplikaci použijte předponu dotazu obsahujícího vložené Python s `set query_python_debug;`
1. Spusťte dotaz.
    * Kusto Explorer: VS Code se automaticky spustí se skriptem *debug_python. py* .
    * Webové uživatelské rozhraní Kusto: 
        1. Stáhněte a uložte *debug_python. py*, *DF. txt*a *kargs. txt*. V okně vyberte možnost **povoleno**. **Uloží** soubory do vybraného adresáře. 

            ![Webové uživatelské rozhraní stáhne vložené soubory Pythonu.](media/debug-inline-python/webui-inline-python.png)

        1. Klikněte pravým tlačítkem na *debug_python. py* a otevřete s vs Code. 
        Skript *debug_python. py* obsahuje vložený kód Pythonu z dotazu KQL, který je předpevněný kódem šablony, aby inicializoval vstupní datový rámec od *DF. txt* a slovníku parametrů z *kargs. txt*.    
            
1. V nástroji VS Code spusťte ladicí program VS Code Debugger: **ladění** > **Spustit ladění (F5)** , vyberte konfigurace **Pythonu** . Ladicí program spustí a automaticky zarážku pro ladění vloženého kódu.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Jak funguje vložené ladění v jazyce Python v VS Code?

1. Dotaz se analyzuje a spustí na serveru, dokud se nedosáhne požadované klauzule `| evaluate python()`.
1. Je vyvolán izolovaný prostor (sandbox) Pythonu, ale místo spuštění kódu je serializována vstupní tabulka, slovník parametrů a kód a pošle se zpátky klientovi.
1. Tyto tři objekty jsou uloženy ve třech souborech: *DF. txt*, *kargs. txt*a *debug_python. py* ve vybraném adresáři (webové uživatelské rozhraní) nebo v adresáři klienta% Temp% (Kusto Explorer).
1. Kód VS se spustí, předem načtený s *debug_python. py* , který obsahuje kód předpony pro inicializaci DF a kargs ze svých příslušných souborů následovaný skriptem Pythonu vloženým do dotazu KQL.

## <a name="query-example"></a>Příklad dotazu

1. Spusťte následující dotaz KQL v klientské aplikaci:

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Podívejte se na výslednou tabulku:

    | x  | X4  |
    |---------|---------|
    | 1\. místo     |   1\. místo      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Spusťte stejný dotaz KQL v klientské aplikaci pomocí `set query_python_debug;`:

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS Code se spustí:

    ![spustit VS Code](media/debug-inline-python/launch-vs-code.png)

1. VS Code ladí a tiskne "výsledek" dataframe v konzole ladění:

    ![Ladění VS Code](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Mezi obrázkem v izolovaném prostoru (sandbox) a místní instalaci může docházet k rozdílům. [Podívejte se na obrázek izolovaného prostoru pro konkrétní balíčky pomocí dotazu na modul plug-in](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl).
