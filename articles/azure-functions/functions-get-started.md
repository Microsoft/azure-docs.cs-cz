---
title: Začínáme se službou Azure Functions
description: Proveďte první kroky k práci s Azure Functions.
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 6aba58dad9853714bf26442592f74fc77e39765c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94975022"
---
# <a name="getting-started-with-azure-functions"></a>Začínáme se službou Azure Functions

## <a name="introduction"></a>Úvod

[Azure Functions](./functions-overview.md) umožňuje implementovat logiku systému do snadno dostupných bloků kódu. Tyto bloky kódu se nazývají "Functions".

Začněte tím, že použijete následující zdroje informací.

::: zone pivot="programming-language-csharp"

| Akce | Zdroje a prostředky |
| --- | --- |
| **Vytvoření první funkce** | Pomocí jednoho z následujících nástrojů:<br><br><li>[Visual Studio Code](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio](./create-first-function-vs-code-csharp.md)<li>[Příkazový řádek](./create-first-function-cli-csharp.md) |
| **Zobrazit spuštěnou funkci** | <li>[Prohlížeč ukázek Azure](https://docs.microsoft.com/samples/browse/?languages=csharp&expanded=azure&products=azure-functions)<li>[Komunitní knihovna Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **Prozkoumejte Interaktivní kurz**| <li>[Volba nejlepší bezserverové technologie Azure pro obchodní scénář](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Dobře navržený rámec – efektivita výkonu](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Spuštění funkce Azure pomocí triggerů](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>[Úplný seznam interaktivních kurzů](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)najdete v tématu Microsoft Learn.|
| **Podrobné informace** | <li>Přečtěte si, jak funkce [automaticky zvyšují nebo zmenšují](./functions-scale.md) instance tak, aby odpovídaly poptávce.<li>Prozkoumejte různé dostupné [metody nasazení](./functions-deployment-technologies.md)<li>Použití integrovaných [nástrojů pro monitorování](./functions-monitoring.md) k analýze vašich funkcí<li>Přečtěte si [referenční informace o jazyce C#](./functions-dotnet-class-library.md)|

::: zone-end

::: zone pivot="programming-language-java"
| Akce | Zdroje a prostředky |
| --- | --- |
| **Vytvoření první funkce** | Pomocí jednoho z následujících nástrojů:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[Funkce Java/Maven s terminálem nebo příkazovým řádkem](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ nápad](./functions-create-maven-intellij.md) |
| **Zobrazit spuštěnou funkci** | <li>[Prohlížeč ukázek Azure](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=java)<li>[Komunitní knihovna Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **Prozkoumejte Interaktivní kurz**| <li>[Volba nejlepší bezserverové technologie Azure pro obchodní scénář](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Dobře navržený rámec – efektivita výkonu](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Vývoj aplikace pomocí modulu plug-in Maven pro Azure Functions](https://docs.microsoft.com/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>[Úplný seznam interaktivních kurzů](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)najdete v tématu Microsoft Learn.|
| **Podrobné informace** | <li>Přečtěte si, jak funkce [automaticky zvyšují nebo zmenšují](./functions-scale.md) instance tak, aby odpovídaly poptávce.<li>Prozkoumejte různé dostupné [metody nasazení](./functions-deployment-technologies.md)<li>Použití integrovaných [nástrojů pro monitorování](./functions-monitoring.md) k analýze vašich funkcí<li>Přečtěte si [referenční informace jazyka Java](./functions-reference-java.md) .|
::: zone-end

::: zone pivot="programming-language-javascript"
| Akce | Zdroje a prostředky |
| --- | --- |
| **Vytvoření první funkce** | Pomocí jednoho z následujících nástrojů:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Node.js terminálu/příkazovém řádku](./create-first-function-cli-java.md) |
| **Zobrazit spuštěnou funkci** | <li>[Prohlížeč ukázek Azure](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=javascript%2Ctypescript)<li>[Komunitní knihovna Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **Prozkoumejte Interaktivní kurz** | <li>[Volba nejlepší bezserverové technologie Azure pro obchodní scénář](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Dobře navržený rámec – efektivita výkonu](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Vytvoření bezserverových rozhraní API pomocí Azure Functions](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Vytvoření logiky bez serveru pomocí služby Azure Functions](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Refaktoring rozhraní API pro Node.js a Express na bezserverová rozhraní API se službou Azure Functions](https://docs.microsoft.com/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>[Úplný seznam interaktivních kurzů](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)najdete v tématu Microsoft Learn.|
| **Podrobné informace** | <li>Přečtěte si, jak funkce [automaticky zvyšují nebo zmenšují](./functions-scale.md) instance tak, aby odpovídaly poptávce.<li>Prozkoumejte různé dostupné [metody nasazení](./functions-deployment-technologies.md)<li>Použití integrovaných [nástrojů pro monitorování](./functions-monitoring.md) k analýze vašich funkcí<li>Přečtěte si referenční informace k jazyku [JavaScript](./functions-reference-node.md) nebo [TypeScript](./functions-reference-node.md#typescript)|
::: zone-end

::: zone pivot="programming-language-powershell"
| Akce | Zdroje a prostředky |
| --- | --- |
| **Vytvoření první funkce** | <li>Použití [Visual Studio Code](./create-first-function-vs-code-powershell.md) |
| **Zobrazit spuštěnou funkci** | <li>[Prohlížeč ukázek Azure](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=powershell)<li>[Komunitní knihovna Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **Prozkoumejte Interaktivní kurz** | <li>[Volba nejlepší bezserverové technologie Azure pro obchodní scénář](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Dobře navržený rámec – efektivita výkonu](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Vytvoření bezserverových rozhraní API pomocí Azure Functions](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Vytvoření logiky bez serveru pomocí služby Azure Functions](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Spuštění funkce Azure pomocí triggerů](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>[Úplný seznam interaktivních kurzů](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)najdete v tématu Microsoft Learn.|
| **Podrobné informace** | <li>Přečtěte si, jak funkce [automaticky zvyšují nebo zmenšují](./functions-scale.md) instance tak, aby odpovídaly poptávce.<li>Prozkoumejte různé dostupné [metody nasazení](./functions-deployment-technologies.md)<li>Použití integrovaných [nástrojů pro monitorování](./functions-monitoring.md) k analýze vašich funkcí<li>Přečtěte si [referenční informace k jazyku PowerShellu](./functions-reference-powershell.md).)|
::: zone-end

::: zone pivot="programming-language-python"
| Akce | Zdroje a prostředky |
| --- | --- |
| **Vytvoření první funkce** | Pomocí jednoho z následujících nástrojů:<br><br><li>[Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-python)<li>[Terminál/příkazový řádek](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python) |
| **Zobrazit spuštěnou funkci** | <li>[Prohlížeč ukázek Azure](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=python)<li>[Komunitní knihovna Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **Prozkoumejte Interaktivní kurz** | <li>[Volba nejlepší bezserverové technologie Azure pro obchodní scénář](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Dobře navržený rámec – efektivita výkonu](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Vytvoření bezserverových rozhraní API pomocí Azure Functions](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Vytvoření logiky bez serveru pomocí služby Azure Functions](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>[Úplný seznam interaktivních kurzů](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)najdete v tématu Microsoft Learn.|
| **Podrobné informace** | <li>Přečtěte si, jak funkce [automaticky zvyšují nebo zmenšují](./functions-scale.md) instance tak, aby odpovídaly poptávce.<li>Prozkoumejte různé dostupné [metody nasazení](./functions-deployment-technologies.md)<li>Použití integrovaných [nástrojů pro monitorování](./functions-monitoring.md) k analýze vašich funkcí<li>Přečtěte si [referenční informace k jazyku Pythonu](./functions-reference-python.md)|
::: zone-end

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o anatomii Azure Functions aplikace](./functions-reference.md)
