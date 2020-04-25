---
title: Porovnání funkcí Azure Stream Analytics
description: Tento článek porovnává funkce podporované Azure Stream Analytics cloudových a IoT Edgech úloh v Azure Portal, Visual Studiu a Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: b169a067f05de422b714e18a4d7ebb9c9e7f0428
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133504"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Porovnání funkcí Azure Stream Analytics

Pomocí Azure Stream Analytics můžete vytvářet streamovaná řešení v cloudu a na IoT Edge pomocí [Azure Portal](stream-analytics-quick-create-portal.md), sady [Visual Studio](stream-analytics-quick-create-vs.md)a [Visual Studio Code](quick-create-vs-code.md). Tabulky v tomto článku ukazují, které funkce jsou podporovány každou platformou pro oba typy úloh.

> [!NOTE]
> Sady Visual Studio a nástroje pro Visual Studio Code nepodporují úlohy v oblastech Čína – východ, Čína – sever, Německo – střed a Německo – severovýchod.

## <a name="cloud-job-features"></a>Funkce cloudové úlohy


|Funkce  |Portál  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Různé platformy     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Vytváření skriptů     |Ano         |Ano         |Ano         |
|Skriptování skriptů IntelliSense     |Zvýrazňování syntaxe         |Zvýrazňování syntaxe</br>Dokončování kódu</br>Značka chyby         |Zvýrazňování syntaxe</br>Dokončování kódu</br>Značka chyby         |
|Definování všech typů vstupů, výstupů a konfigurací úloh     |Ano         |Ano         |Ano         |
|Správa zdrojového kódu     |Ne         |Ano         |Ano         |
|Podpora CI/CD     |Částečné         |Ano         |Ano         |
|Sdílení vstupů a výstupů napříč více dotazy     |Ne         |Ano         |Ano         |
|Dotaz na testování pomocí ukázkového souboru     |Ano         |Ano        |Ano         |
|Lokální testování dat za provozu     |Ne         |Ano       |Ano      |
|Vypsání úloh a zobrazení entit úloh     |Ano         |Ano        |Ano         |
|Export úlohy do místního projektu     |Ne         |Ano         |Ano         |
|Odesílání, spouštění a zastavování úloh     |Ano         |Ano         |Ano         |
|Zobrazit metriky úlohy a diagram     |Ano         |Ano         |Otevřít na portálu         |
|Zobrazit chyby běhu úlohy     |Ano         |Ano         |Ne         |
|Protokoly prostředků     |Ano         |Ne         |Ne         |
|Vlastní vlastnosti zprávy     |Ano         |Ano         |Ne       |
|Funkce vlastního kódu a deserializace jazyka C#|Režim jen pro čtení|Ano|Ne|
|JavaScript UDF a UDA     |Ano         |Ano         |Jen ve Windows         |
|Služba Machine Learning     |Ano        |Ano         |Ne         |
|Machine Learning Studio     |Ano, ale dotaz se nedá testovat.        |Ano |Ne         |
|Úroveň kompatibility     |1.0</br>1.1</br>1,2 (výchozí)         |1.0</br>1.1</br>1,2 (výchozí)           |1.0</br>1.1</br>1,2 (výchozí)           |
|Integrované funkce detekce anomálií založené na ML     |Ano         |Ano         |Ano         |
|Předdefinované geoprostorové funkce     |Ano         |Ano         |Ano         |



## <a name="iot-edge-job-features"></a>Funkce úlohy IoT Edge

|Funkce  |Portál  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Vytváření úloh     |Ano         |Ano         |Ne         |
|Správa zdrojového kódu     |Ne         |Ano         |Ne         |
|Export úlohy do místního projektu     |Ne         |Ano         |Ne         |
|Dotaz na testování pomocí ukázkového souboru     |Ano         |Ano         |Ne         |
|Sdílení vstupů a výstupů napříč více dotazy     |Ne         |Ano         |Ne         |
|Uživatelsky definovaná funkce v C#     |Ne         |Ano         |Ne         |
|Odeslat úlohy     |Ano         |Ano         |Ne         |
|Vypsání úloh a zobrazení entit úloh     |Ano         |Ano         |Ne         |
|Zobrazit metriky úlohy a diagram     |Ano         |Částečné         |Ne         |
|Zobrazit chyby běhu úlohy     |Ano         |Částečné         |Ne         |
|Podpora CI/CD     |Ne         |Ne         |Ne         |


## <a name="next-steps"></a>Další kroky

* [Azure Stream Analytics na hraničních zařízeních IoT](stream-analytics-edge.md)
* [Kurz: zápis uživatelsky definované funkce jazyka C# pro Azure Stream Analytics úlohy IoT Edge (Preview)](stream-analytics-edge-csharp-udf.md)
* [Vývoj úloh Stream Analytics IoT Edge pomocí nástrojů sady Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Použití sady Visual Studio k zobrazení Azure Stream Analyticsch úloh](stream-analytics-vs-tools.md)
* [Prozkoumat Azure Stream Analytics s využitím Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md)


