---
title: Ladění uživatelem definovaných funkcí v Azure Stream Analytics
description: Tento článek popisuje, jak ladit uživatelsky definované funkce v Azure Stream Analytics.
author: jenssuessmeyer
ms.author: jenss
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/24/2020
ms.custom: devx-track-js
ms.openlocfilehash: 567def7abaff3cd8d70eb56f0aa117d1eeb52a13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91300424"
---
# <a name="debug-user-defined-functions-in-azure-stream-analytics"></a>Ladění uživatelem definovaných funkcí v Azure Stream Analytics 

Pokud uživatelsky definované funkce (UDF) nefungují podle očekávání, je nutné je ladit, abyste zjistili problém. Můžete ladit UDF pro úlohy Stream Analytics, když spouštíte úlohy místně pomocí [Visual Studio Code](visual-studio-code-local-run-live-input.md) nebo sady [Visual Studio](stream-analytics-vs-tools-local-run.md).

Když svou úlohu spustíte místně na aktivním vstupním streamu, napodobá se jenom spuštění cloudového Azure Stream Analyticsho modulu na jednom uzlu. Lokální testování dat v reálném čase nemůže nahradit testování výkonu a škálovatelnosti, které v cloudu provedete, ale během funkčního testování ušetříte čas tím, že nebudete muset posílat úlohy do cloudu pokaždé, když chcete testovat. Kromě toho jsou zásady času pro místní spuštění s místními nebo ukázkovými daty zakázané, ale pro testování dynamických dat se podporují časové zásady.

## <a name="pick-your-language"></a>Výběr jazyka

UDF můžete napsat pro Azure Stream Analytics pomocí rozhraní .NET (C#) nebo JavaScriptu. 

### <a name="functions-in-c"></a>Funkce v jazyce C # 

Při [psaní .NET UDF pomocí sady Visual Studio](stream-analytics-edge-csharp-udf-methods.md)získáte stejnou úroveň podpory jako u jakéhokoli projektu třídy .NET. Tato podpora zahrnuje:

* Podpora kompilace, jako je například kontrola syntaxe a podpora kompilátoru.

* Možnost přidávat, sestavovat a odkazovat na projekt C# a artefakty ve vašem řešení Stream Analytics. 

* Snadné opakované použití kódu zapouzdřeného v projektu, který lze sdílet. 

* Ladit podporu přímo v aplikaci Visual Studio. Nastavte Stream Analytics projekt jako spustit projekt a nastavte zarážky v kódu jazyka C#. Pak stiskněte klávesu **F5** pro ladění kódu C# stejně jako pro jakýkoli jiný projekt C#. 

### <a name="functions-in-javascript"></a>Funkce v JavaScriptu

JavaScript je další možnost pro vytváření funkcí v Stream Analytics. Kód jazyka JavaScript je přímo umístěn do oblasti funkcí projektu Stream Analytics, který usnadňuje sdílení v projektech.

K kompilaci dojde, když je projekt Stream Analytics kompilován nebo proveden. Možnost nalezení problému pouze za běhu je vyšší. V Stream Analytics není podporována žádná podpora ladění pro funkce JavaScriptu.

## <a name="debug-options-for-javascript"></a>Možnosti ladění pro JavaScript

Vzhledem k tomu, že žádná podpora ladění pro funkce JavaScriptu v Stream Analytics přímo, můžete ladit zapouzdřením funkce na webu HTML a získání výstupu z něj.

Následující příklad ukazuje, jak ladit JavaScript UDF s určitými omezeními v integrovaném běhovém prostředí v [Visual Studio Code](quick-create-visual-studio-code.md).

### <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že Azure Stream Analytics projekt obsahuje následující položky:

* Vstup 
* Výstup 
* Stream Analytics dotaz (. asaql) 
* Konfigurace úlohy Stream Analytics (JobConfig.jszapnuto)
* JavaScript UDF

### <a name="prepare-files"></a>Příprava souborů

Na následujícím obrázku soubor dotazu *. asaql* obsahuje pouze volání UDF, *fxcharCount*. Tato změna zajistí, že po provedení změn budete moct projekt zkompilovat.

> [!div class="mx-imgBorder"]
> ![Soubor testovacího dotazu Stream Analytics](./media/debug-user-defined-functions/asaql-file.png)

Vytvořte další složku v **testech** pro hostování testovacího souboru, který je volán pro provedení testu pomocí funkce JavaScriptu. V tomto příkladu je název složky *fxcharCount* a název testu je *Test_UDF.js*. 

Následující obrázek ukazuje kód v testovacím souboru, který načte soubor funkce a spustí funkci. Tento příklad je jednoduchý, ale můžete načíst další soubory testovacích dat a smyčky, i když další testy pro získání výstupu. Notace volání funkce se trochu liší od běžných volání, protože se na soubor odkazuje a není načten do modulu runtime, což umožňuje ladění. 

> [!div class="mx-imgBorder"]
> ![Soubor testovacího Stream Analytics](./media/debug-user-defined-functions/test-file.png)

Ve funkci přidejte do souboru následující řádky kódu k vystavení metod. Neovlivňují schopnost kompilovat kód v Visual Studio Code.

```javascript
var methods = {};
methods.fxchartCount = fxchartCount;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![Stream Analytics JavaScript UDF](./media/debug-user-defined-functions/udf-file.png)
  
## <a name="install-debug-support"></a>Nainstalovat podporu ladění

Chcete-li ladit, je nutné [Stáhnout](https://nodejs.org/en/download/) a nainstalovat **node.js**. Nainstalujte správnou verzi podle platformy, kterou používáte. Až nainstalujete modul runtime node.js, restartujte Visual Studio Code, aby se změny implementovaly. 

Vyberte **Spustit a ladit** nebo stiskněte klávesy **CTRL + SHIFT + D** a spusťte ladění. Zobrazí se pole se seznamem, kde můžete vybrat **node.js** jako modul runtime. Pokud máte nainstalovanou jenom node.js, použije se ve výchozím nastavení. V případě potřeby byste měli být schopni krokovat kód a do satelitního souboru, pokud to vyžaduje klávesu F11. 

> [!div class="mx-imgBorder"]
> ![Stream Analytics spustit a ladit systém souborů UDF](./media/debug-user-defined-functions/run-debug-udf.png)

### <a name="debug-user-defined-aggregates"></a>Ladit uživatelsky definované agregace 

Pomocí metody Debug pro JavaScript UDF můžete ladit uživatelsky definované agregace (UDA). V tomto příkladu je přidána UDA do souboru dotazu *. asaql* a testovací soubor.

Stejně jako u systému souborů UDF zadáte volání do UDA, aby se zajistilo, že se projekt zkompiluje po provedení změn. 

> [!div class="mx-imgBorder"]
> ![Přidání Uda do asaql](./media/debug-user-defined-functions/asaql-uda.png)

V souboru *Test_UDA.js* odkazujete na soubor Uda, který jste provedli se systémem souborů UDF. Kromě toho zavoláte `main()` , `init()` a `accumulate()` . `accumulate()`Metoda je volána ve smyčce pro vložení hodnot do zásobníku stavů. `computeresult()`Metoda je volána k vytvoření finálního dotazu. 

> [!div class="mx-imgBorder"]
> ![Testovací soubor UDA](./media/debug-user-defined-functions/uda-test.png)

Stejně jako v příkladu UDF je třeba do samotného UDA přidat nějaký kód k vystavení relevantních metod.

```javascript
var methods = {};
methods.main = main;
methods.init = main.init;
methods.accumulate = main.accumulate;
methods.computeResult = main.computeResult;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![Kód přidaný do UDA](./media/debug-user-defined-functions/uda-expose-methods.png)

Vyberte **Spustit a ladit** nebo stiskněte klávesy **CTRL + SHIFT + D** a spusťte ladění. Zobrazí se pole se seznamem, kde můžete vybrat **node.js** jako modul runtime. Pokud máte nainstalovanou jenom node.js, použije se ve výchozím nastavení. V případě potřeby byste měli být schopni krokovat kód a do satelitního souboru, pokud to vyžaduje klávesu F11.

> [!div class="mx-imgBorder"]
> ![Stream Analytics spuštění a ladění Uda](./media/debug-user-defined-functions/run-debug-uda.png)


## <a name="next-steps"></a>Další kroky

* [Místní vývoj a ladění úloh Azure Stream Analytics](develop-locally.md)
* [Ladění Azure Stream Analytics dotazů místně pomocí diagramu úloh v aplikaci Visual Studio](debug-locally-using-job-diagram.md)
* [Uživatelsky definované funkce v Azure Stream Analytics](functions-overview.md)
 
