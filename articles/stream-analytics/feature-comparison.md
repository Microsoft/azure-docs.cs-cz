---
title: Porovnání funkcí Azure Stream Analytics
description: Tento článek porovnává funkce podporované Azure Stream Analytics cloudových a IoT Edgech úloh v Azure Portal, Visual Studiu a Visual Studio Code.
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 037bd8bc823cd8c77241d0ca25174e29d25149b9
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020532"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Porovnání funkcí Azure Stream Analytics

Pomocí Azure Stream Analytics můžete vytvářet streamovaná řešení v cloudu a na IoT Edge pomocí [Azure Portal](stream-analytics-quick-create-portal.md), sady [Visual Studio](stream-analytics-quick-create-vs.md)a [Visual Studio Code](quick-create-visual-studio-code.md). Tabulky v tomto článku ukazují, které funkce jsou podporovány každou platformou pro oba typy úloh.

> [!NOTE]
> Sady Visual Studio a nástroje pro Visual Studio Code nepodporují úlohy v oblastech Čína – východ, Čína – sever, Německo – střed a Německo – severovýchod.

## <a name="cloud-job-features"></a>Funkce cloudové úlohy


|Příznak  |Portál  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Různé platformy     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Vytváření skriptů     |Ano         |Ano         |Ano         |
|Skriptování skriptů IntelliSense     |Zvýrazňování syntaxe         |Zvýrazňování syntaxe</br>Dokončování kódu</br>Značka chyby         |Zvýrazňování syntaxe</br>Dokončování kódu</br>Značka chyby         |
|Definování všech typů vstupů, výstupů a konfigurací úloh     |Ano         |Ano         |Ano         |
|Správa zdrojového kódu     |No         |Ano         |Ano         |
|Podpora CI/CD     |Částečná         |Ano         |Ano         |
|Sdílení vstupů a výstupů napříč více dotazy     |No         |Ano         |Ano         |
|Dotaz na testování pomocí ukázkového souboru     |Ano         |Ano        |Ano         |
|Lokální testování dat za provozu     |No         |Ano       |Ano      |
|Vypsání úloh a zobrazení entit úloh     |Ano         |Ano        |Ano         |
|Export úlohy do místního projektu     |No         |Ano         |Ano         |
|Odesílání, spouštění a zastavování úloh     |Ano         |Ano         |Ano         |
|Zobrazit metriky úlohy a diagram     |Ano         |Ano         |Otevřít na portálu         |
|Zobrazit chyby běhu úlohy     |Ano         |Ano         |No         |
|Protokoly prostředků     |Ano         |No         |No         |
|Vlastní vlastnosti zprávy     |Ano         |Ano         |No       |
|Funkce vlastního kódu a deserializace jazyka C#|Režim jen pro čtení|Ano|No|
|JavaScript UDF a UDA     |Ano         |Ano         |Jen ve Windows         |
|Machine Learning Service     |Ano        |Ano         |No         |
|Azure Machine Learning Studio (klasický)|Ano, ale dotaz se nedá testovat.        |Ano |No         |
|Úroveň kompatibility     |1.0</br>1,1</br>1,2 (výchozí)         |1.0</br>1,1</br>1,2 (výchozí)           |1.0</br>1,1</br>1,2 (výchozí)           |
|Integrované funkce detekce anomálií založené na ML     |Ano         |Ano         |Ano         |
|Předdefinované geoprostorové funkce     |Ano         |Ano         |Ano         |



## <a name="iot-edge-job-features"></a>Funkce úlohy IoT Edge

|Příznak  |Portál  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Vytváření úloh     |Ano         |Ano         |No         |
|Správa zdrojového kódu     |No         |Ano         |No         |
|Export úlohy do místního projektu     |No         |Ano         |No         |
|Dotaz na testování pomocí ukázkového souboru     |Ano         |Ano         |No         |
|Sdílení vstupů a výstupů napříč více dotazy     |No         |Ano         |No         |
|Uživatelsky definovaná funkce v C#     |No         |Ano         |No         |
|Odeslat úlohy     |Ano         |Ano         |No         |
|Vypsání úloh a zobrazení entit úloh     |Ano         |Ano         |No         |
|Zobrazit metriky úlohy a diagram     |Ano         |Částečná         |No         |
|Zobrazit chyby běhu úlohy     |Ano         |Částečná         |No         |
|Podpora CI/CD     |No         |No         |No         |


## <a name="next-steps"></a>Další kroky

* [Azure Stream Analytics na hraničních zařízeních IoT](stream-analytics-edge.md)
* [Kurz: zápis uživatelsky definované funkce jazyka C# pro Azure Stream Analytics úlohy IoT Edge (Preview)](stream-analytics-edge-csharp-udf.md)
* [Vývoj úloh Stream Analytics IoT Edge pomocí nástrojů sady Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Použití sady Visual Studio k zobrazení Azure Stream Analyticsch úloh](stream-analytics-vs-tools.md)
* [Prozkoumat Azure Stream Analytics s využitím Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md)


