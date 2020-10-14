---
title: Porovnání funkcí Azure Stream Analytics
description: Tento článek porovnává funkce podporované Azure Stream Analytics cloudových a IoT Edgech úloh v Azure Portal, Visual Studiu a Visual Studio Code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 2fbaaac0095622f14d5bc0033b7787bec8ec2fa2
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018283"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Porovnání funkcí Azure Stream Analytics

Pomocí Azure Stream Analytics můžete vytvářet streamovaná řešení v cloudu a na IoT Edge pomocí [Azure Portal](stream-analytics-quick-create-portal.md), sady [Visual Studio](stream-analytics-quick-create-vs.md)a [Visual Studio Code](quick-create-visual-studio-code.md). Tabulky v tomto článku ukazují, které funkce jsou podporovány každou platformou pro oba typy úloh.

> [!NOTE]
> Sady Visual Studio a nástroje pro Visual Studio Code nepodporují úlohy v oblastech Čína – východ, Čína – sever, Německo – střed a Německo – severovýchod.

## <a name="cloud-job-features"></a>Funkce cloudové úlohy


|Funkce  |Portál  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Různé platformy     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Vytváření skriptů     |Yes         |Yes         |Yes         |
|Skriptování skriptů IntelliSense     |Zvýrazňování syntaxe         |Zvýrazňování syntaxe</br>Dokončování kódu</br>Značka chyby         |Zvýrazňování syntaxe</br>Dokončování kódu</br>Značka chyby         |
|Definování všech typů vstupů, výstupů a konfigurací úloh     |Yes         |Yes         |Yes         |
|Správa zdrojového kódu     |No         |Yes         |Yes         |
|Podpora CI/CD     |Částečné         |Yes         |Yes         |
|Sdílení vstupů a výstupů napříč více dotazy     |No         |Yes         |Yes         |
|Dotaz na testování pomocí ukázkového souboru     |Yes         |Yes        |Yes         |
|Lokální testování dat za provozu     |No         |Yes       |Yes      |
|Vypsání úloh a zobrazení entit úloh     |Yes         |Yes        |Yes         |
|Export úlohy do místního projektu     |No         |Yes         |Yes         |
|Odesílání, spouštění a zastavování úloh     |Yes         |Yes         |Yes         |
|Zobrazit metriky úlohy a diagram     |Yes         |Yes         |Otevřít na portálu         |
|Zobrazit chyby běhu úlohy     |Yes         |Yes         |No         |
|Protokoly prostředků     |Yes         |No         |No         |
|Vlastní vlastnosti zprávy     |Yes         |Yes         |No       |
|Funkce vlastního kódu a deserializace jazyka C#|Režim jen pro čtení|Yes|No|
|JavaScript UDF a UDA     |Yes         |Yes         |Jen ve Windows         |
|Machine Learning Service     |Yes        |Yes         |No         |
|Azure Machine Learning Studio (klasický)|Ano, ale dotaz se nedá testovat.        |Yes |No         |
|Úroveň kompatibility     |1,0</br>1,1</br>1,2 (výchozí)         |1,0</br>1,1</br>1,2 (výchozí)           |1,0</br>1,1</br>1,2 (výchozí)           |
|Integrované funkce detekce anomálií založené na ML     |Yes         |Yes         |Yes         |
|Předdefinované geoprostorové funkce     |Yes         |Yes         |Yes         |



## <a name="iot-edge-job-features"></a>Funkce úlohy IoT Edge

|Funkce  |Portál  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Vytváření úloh     |Yes         |Yes         |No         |
|Správa zdrojového kódu     |No         |Yes         |No         |
|Export úlohy do místního projektu     |No         |Yes         |No         |
|Dotaz na testování pomocí ukázkového souboru     |Yes         |Yes         |No         |
|Sdílení vstupů a výstupů napříč více dotazy     |No         |Yes         |No         |
|Uživatelsky definovaná funkce v C#     |No         |Yes         |No         |
|Odeslat úlohy     |Yes         |Yes         |No         |
|Vypsání úloh a zobrazení entit úloh     |Yes         |Yes         |No         |
|Zobrazit metriky úlohy a diagram     |Yes         |Částečné         |No         |
|Zobrazit chyby běhu úlohy     |Yes         |Částečné         |No         |
|Podpora CI/CD     |No         |No         |No         |


## <a name="next-steps"></a>Další kroky

* [Azure Stream Analytics na hraničních zařízeních IoT](stream-analytics-edge.md)
* [Kurz: zápis uživatelsky definované funkce jazyka C# pro Azure Stream Analytics úlohy IoT Edge (Preview)](stream-analytics-edge-csharp-udf.md)
* [Vývoj úloh Stream Analytics IoT Edge pomocí nástrojů sady Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Použití sady Visual Studio k zobrazení Azure Stream Analyticsch úloh](stream-analytics-vs-tools.md)
* [Prozkoumat Azure Stream Analytics s využitím Visual Studio Code (Preview)](visual-studio-code-explore-jobs.md)


