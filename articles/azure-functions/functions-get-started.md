---
title: Začínáme se službou Azure Functions
description: Proveďte první kroky k práci s Azure Functions.
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: e969cc4bee9f09f4279d137abb33b75ca0672ffc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364711"
---
# <a name="getting-started-with-azure-functions"></a>Začínáme se službou Azure Functions

## <a name="introduction"></a>Úvod

[Azure Functions](./functions-overview.md) umožňuje implementovat logiku systému do snadno dostupných bloků kódu. Tyto bloky kódu se nazývají "Functions".

Začněte tím, že použijete následující zdroje informací.

::: zone pivot="programming-language-csharp"

| Akce | Zdroje informací |
| --- | --- |
| **Vytvoření první funkce** | Pomocí jednoho z následujících nástrojů:<br><br><li>[Visual Studio](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio Code](./create-first-function-vs-code-csharp.md)<li>[Příkazový řádek](./create-first-function-cli-csharp.md) |
| **Zobrazit spuštěnou funkci** | <li>[Prohlížeč ukázek Azure](/samples/browse/?expanded=azure&languages=csharp&products=azure-functions)<li>[Komunitní knihovna Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **Prozkoumejte Interaktivní kurz**| <li>[Volba nejlepší bezserverové technologie Azure pro obchodní scénář](/learn/modules/serverless-fundamentals/)<li>[Dobře navržený rámec – efektivita výkonu](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Spuštění funkce Azure pomocí triggerů](/learn/modules/execute-azure-function-with-triggers/) <br><br>[Úplný seznam interaktivních kurzů](/learn/browse/?expanded=azure&products=azure-functions)najdete v tématu Microsoft Learn.|
| **Kontrola osvědčených postupů** |<li>[Výkon a spolehlivost](./functions-best-practices.md)<li>[Správa připojení](./manage-connections.md)<li>[Zpracování chyb a opakované pokusy funkcí](./functions-bindings-error-pages.md?tabs=csharp)<li>[Zabezpečení](./security-concepts.md)|
| **Podrobné informace** | <li>Přečtěte si, jak funkce [automaticky zvyšují nebo zmenšují](./functions-scale.md) instance tak, aby odpovídaly poptávce.<li>Prozkoumejte různé dostupné [metody nasazení](./functions-deployment-technologies.md)<li>Použití integrovaných [nástrojů pro monitorování](./functions-monitoring.md) k analýze vašich funkcí<li>Přečtěte si [referenční informace o jazyce C#](./functions-dotnet-class-library.md)|

::: zone-end

::: zone pivot="programming-language-java"
| Akce | Zdroje informací |
| --- | --- |
| **Vytvoření první funkce** | Pomocí jednoho z následujících nástrojů:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[Funkce Java/Maven s terminálem nebo příkazovým řádkem](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ IDEA](./functions-create-maven-intellij.md) |
| **Zobrazit spuštěnou funkci** | <li>[Prohlížeč ukázek Azure](/samples/browse/?expanded=azure&languages=java&products=azure-functions)<li>[Komunitní knihovna Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **Prozkoumejte Interaktivní kurz**| <li>[Volba nejlepší bezserverové technologie Azure pro obchodní scénář](/learn/modules/serverless-fundamentals/)<li>[Dobře navržený rámec – efektivita výkonu](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Vývoj aplikace pomocí modulu plug-in Maven pro Azure Functions](/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>[Úplný seznam interaktivních kurzů](/learn/browse/?expanded=azure&products=azure-functions)najdete v tématu Microsoft Learn.|
| **Kontrola osvědčených postupů** |<li>[Výkon a spolehlivost](./functions-best-practices.md)<li>[Správa připojení](./manage-connections.md)<li>[Zpracování chyb a opakované pokusy funkcí](./functions-bindings-error-pages.md?tabs=java)<li>[Zabezpečení](./security-concepts.md)|
| **Podrobné informace** | <li>Přečtěte si, jak funkce [automaticky zvyšují nebo zmenšují](./functions-scale.md) instance tak, aby odpovídaly poptávce.<li>Prozkoumejte různé dostupné [metody nasazení](./functions-deployment-technologies.md)<li>Použití integrovaných [nástrojů pro monitorování](./functions-monitoring.md) k analýze vašich funkcí<li>Přečtěte si [referenční informace jazyka Java](./functions-reference-java.md) .|
::: zone-end

::: zone pivot="programming-language-javascript"
| Akce | Zdroje informací |
| --- | --- |
| **Vytvoření první funkce** | Pomocí jednoho z následujících nástrojů:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Node.js terminálu/příkazovém řádku](./create-first-function-cli-java.md) |
| **Zobrazit spuštěnou funkci** | <li>[Prohlížeč ukázek Azure](/samples/browse/?expanded=azure&languages=javascript%2ctypescript&products=azure-functions)<li>[Komunitní knihovna Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **Prozkoumejte Interaktivní kurz** | <li>[Volba nejlepší bezserverové technologie Azure pro obchodní scénář](/learn/modules/serverless-fundamentals/)<li>[Dobře navržený rámec – efektivita výkonu](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Vytvoření bezserverových rozhraní API pomocí Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Vytvoření logiky bez serveru pomocí služby Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Refaktoring rozhraní API pro Node.js a Express na bezserverová rozhraní API se službou Azure Functions](/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>[Úplný seznam interaktivních kurzů](/learn/browse/?expanded=azure&products=azure-functions)najdete v tématu Microsoft Learn.|
| **Kontrola osvědčených postupů** |<li>[Výkon a spolehlivost](./functions-best-practices.md)<li>[Správa připojení](./manage-connections.md)<li>[Zpracování chyb a opakované pokusy funkcí](./functions-bindings-error-pages.md?tabs=javascript)<li>[Zabezpečení](./security-concepts.md)|
| **Podrobné informace** | <li>Přečtěte si, jak funkce [automaticky zvyšují nebo zmenšují](./functions-scale.md) instance tak, aby odpovídaly poptávce.<li>Prozkoumejte různé dostupné [metody nasazení](./functions-deployment-technologies.md)<li>Použití integrovaných [nástrojů pro monitorování](./functions-monitoring.md) k analýze vašich funkcí<li>Přečtěte si referenční informace k jazyku [JavaScript](./functions-reference-node.md) nebo [TypeScript](./functions-reference-node.md#typescript)|
::: zone-end

::: zone pivot="programming-language-powershell"
| Akce | Zdroje informací |
| --- | --- |
| **Vytvoření první funkce** | <li>Použití [Visual Studio Code](./create-first-function-vs-code-powershell.md) |
| **Zobrazit spuštěnou funkci** | <li>[Prohlížeč ukázek Azure](/samples/browse/?expanded=azure&languages=powershell&products=azure-functions)<li>[Komunitní knihovna Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **Prozkoumejte Interaktivní kurz** | <li>[Volba nejlepší bezserverové technologie Azure pro obchodní scénář](/learn/modules/serverless-fundamentals/)<li>[Dobře navržený rámec – efektivita výkonu](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Vytvoření bezserverových rozhraní API pomocí Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Vytvoření logiky bez serveru pomocí služby Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Spuštění funkce Azure pomocí triggerů](/learn/modules/execute-azure-function-with-triggers/) <br><br>[Úplný seznam interaktivních kurzů](/learn/browse/?expanded=azure&products=azure-functions)najdete v tématu Microsoft Learn.|
| **Kontrola osvědčených postupů** |<li>[Výkon a spolehlivost](./functions-best-practices.md)<li>[Správa připojení](./manage-connections.md)<li>[Zpracování chyb a opakované pokusy funkcí](./functions-bindings-error-pages.md?tabs=powershell)<li>[Zabezpečení](./security-concepts.md)|
| **Podrobné informace** | <li>Přečtěte si, jak funkce [automaticky zvyšují nebo zmenšují](./functions-scale.md) instance tak, aby odpovídaly poptávce.<li>Prozkoumejte různé dostupné [metody nasazení](./functions-deployment-technologies.md)<li>Použití integrovaných [nástrojů pro monitorování](./functions-monitoring.md) k analýze vašich funkcí<li>Přečtěte si [referenční informace k jazyku PowerShellu](./functions-reference-powershell.md).)|
::: zone-end

::: zone pivot="programming-language-python"
| Akce | Zdroje informací |
| --- | --- |
| **Vytvoření první funkce** | Pomocí jednoho z následujících nástrojů:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)<li>[Terminál/příkazový řádek](./create-first-function-cli-csharp.md?pivots=programming-language-python) |
| **Zobrazit spuštěnou funkci** | <li>[Prohlížeč ukázek Azure](/samples/browse/?expanded=azure&languages=python&products=azure-functions)<li>[Komunitní knihovna Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **Prozkoumejte Interaktivní kurz** | <li>[Volba nejlepší bezserverové technologie Azure pro obchodní scénář](/learn/modules/serverless-fundamentals/)<li>[Dobře navržený rámec – efektivita výkonu](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Vytvoření bezserverových rozhraní API pomocí Azure Functions](/learn/modules/build-api-azure-functions/)<li>[Vytvoření logiky bez serveru pomocí služby Azure Functions](/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>[Úplný seznam interaktivních kurzů](/learn/browse/?expanded=azure&products=azure-functions)najdete v tématu Microsoft Learn.|
| **Kontrola osvědčených postupů** |<li>[Výkon a spolehlivost](./functions-best-practices.md)<li>[Správa připojení](./manage-connections.md)<li>[Zpracování chyb a opakované pokusy funkcí](./functions-bindings-error-pages.md?tabs=python)<li>[Zabezpečení](./security-concepts.md)<li>[Zvýšení výkonu propustnosti](./python-scale-performance-reference.md)|
| **Podrobné informace** | <li>Přečtěte si, jak funkce [automaticky zvyšují nebo zmenšují](./functions-scale.md) instance tak, aby odpovídaly poptávce.<li>Prozkoumejte různé dostupné [metody nasazení](./functions-deployment-technologies.md)<li>Použití integrovaných [nástrojů pro monitorování](./functions-monitoring.md) k analýze vašich funkcí<li>Přečtěte si [referenční informace k jazyku Pythonu](./functions-reference-python.md)|
::: zone-end

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o anatomii Azure Functions aplikace](./functions-reference.md)
