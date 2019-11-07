---
title: Porovnání funkcí Azure Stream Analytics
description: Tento článek porovnává funkce podporované Azure Stream Analytics cloudových a IoT Edgech úloh v Azure Portal, Visual Studiu a Visual Studio Code.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 4eb19a5b344cc5bda5ecad724daaddf9b0000d7e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580909"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Porovnání funkcí Azure Stream Analytics

Pomocí Azure Stream Analytics můžete vytvářet streamovaná řešení v cloudu a na IoT Edge pomocí [Azure Portal](stream-analytics-quick-create-portal.md), sady [Visual Studio](stream-analytics-quick-create-vs.md)a [Visual Studio Code](quick-create-vs-code.md). Tabulky v tomto článku ukazují, které funkce jsou podporovány každou platformou pro oba typy úloh.

## <a name="cloud-job-features"></a>Funkce cloudové úlohy


|Funkce  |Portál  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Různé platformy     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Vytváření skriptů     |Ano         |Ano         |Ano         |
|Skriptování skriptů IntelliSense     |Zvýrazňování syntaxe         |Zvýrazňování syntaxe</br>Dokončování kódu</br>Značka chyby         |Zvýrazňování syntaxe</br>Dokončování kódu</br>Značka chyby         |
|Definování vstupů, výstupů a konfigurací úloh     |Ano         |Ano         |Ano         |
|Vytváření oddílů výstupu objektů BLOB     |Ano         |Ano         |Ano         |
|Power BI jako výstup     |Ano         |Ano         |Ne         |
|Referenční data SQL Database     |Ano         |Ano         |Ano         |
|Vlastní vlastnosti zprávy     |Ano         |Ne         |Ne         |
|Sdílení vstupů a výstupů napříč více dotazy     |Ne         |Ano         |Ano         |
|JavaScript UDF a UDA     |Ano         |Ano         |Pouze Windows         |
|Popisky Machine Learning     |Ano, ale dotaz se nedá testovat.        |Ano, ale nelze je místně testovat         |Ne         |
|Úroveň kompatibility     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|Integrované funkce detekce anomálií založené na ML     |Ano         |Ano         |Ano         |
|Předdefinované geoprostorové funkce     |Ano         |Ano         |Ano         |
|Dotaz na testování pomocí ukázkového souboru     |Ano         |Ano         |Ano         |
|Lokální testování dat za provozu     |Ne         |Ano         |Ne         |
|Vypsání úloh a zobrazení entit úloh     |Ano         |Ano         |Ano         |
|Export úlohy do místního projektu     |Ne         |Ano         |Ano         |
|Odesílání, spouštění a zastavování úloh     |Ano         |Ano         |Ano         |
|Správa zdrojového kódu     |Ne         |Ano         |Ano         |
|Podpora CI/CD     |Částečné         |Ano         |Ano         |
|Zobrazit metriky úlohy a diagram     |Ano         |Ano         |Otevřít na portálu         |
|Zobrazit chyby běhu úlohy     |Ano         |Ano         |Ne         |
|Diagnostické protokoly     |Ano         |Ne         |Ne         |


## <a name="iot-edge-job-features"></a>Funkce úlohy IoT Edge

|Funkce  |Portál  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Vytváření úloh     |Ano         |Ano         |Ne         |
|Správa zdrojového kódu     |Ne         |Ano         |Ne         |
|Export úlohy do místního projektu     |Ne         |Ano         |Ne         |
|Dotaz na testování pomocí ukázkového souboru     |Ano         |Ano         |Ne         |
|Sdílení vstupů a výstupů napříč více dotazy     |Ne         |Ano         |Ne         |
|C#SOUBORŮ     |Ne         |Ano         |Ne         |
|Odeslat úlohy     |Ano         |Ano         |Ne         |
|Vypsání úloh a zobrazení entit úloh     |Ano         |Ano         |Ne         |
|Zobrazit metriky úlohy a diagram     |Ano         |Částečné         |Ne         |
|Zobrazit chyby běhu úlohy     |Ano         |Částečné         |Ne         |
|Podpora CI/CD     |Ne         |Ne         |Ne         |


## <a name="next-steps"></a>Další kroky

* [Azure Stream Analytics na IoT Edge](stream-analytics-edge.md)
* [Kurz: zápis C# uživatelsky definované funkce pro úlohu Azure Stream Analytics IoT Edge (Preview)](stream-analytics-edge-csharp-udf.md)
* [Vývoj úloh Stream Analytics IoT Edge pomocí nástrojů sady Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Použití sady Visual Studio k zobrazení Azure Stream Analyticsch úloh](stream-analytics-vs-tools.md)
* [Prozkoumat Azure Stream Analytics s využitím Visual Studio Code (Preview)](vscode-explore-jobs.md)


