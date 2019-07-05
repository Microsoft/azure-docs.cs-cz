---
title: Porovnání funkcí služby Azure Stream Analytics
description: Tento článek porovnává funkce podporované pro cloud Azure Stream Analytics a úlohy IoT Edge webu Azure portal, sady Visual Studio a Visual Studio Code.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 22d7ef90ee0cf4d09467516b7bb0664327b7dabe
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509778"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Porovnání funkcí služby Azure Stream Analytics

Azure Stream Analytics můžete vytvářet řešení streamování v cloudu i na hraničních zařízeních IoT pomocí [webu Azure portal](stream-analytics-quick-create-portal.md), [sady Visual Studio](stream-analytics-quick-create-vs.md), a [Visual Studio Code](quick-create-vs-code.md). Tabulky v tomto článku ukazují, funkcí, které podporují jednotlivé platformy pro oba typy úloh.

## <a name="cloud-job-features"></a>Funkce úlohy v cloudu


|Funkce  |Portál  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Různé platformy     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Vytváření skriptu     |Ano         |Ano         |Ano         |
|Skript Intellisense     |Zvýrazňování syntaxe         |Zvýrazňování syntaxe</br>Doplňování kódu</br>Chyba značky         |Zvýrazňování syntaxe</br>Doplňování kódu</br>Chyba značky         |
|Definování vstupy, výstupy a úlohy konfigurace     |Ano         |Ano         |Ano         |
|Vytváření oddílů výstupního objektu BLOB     |Ano         |Ano         |Ano         |
|Power BI jako výstup     |Ano         |Ano         |Ne         |
|SQL database referenčních dat     |Ano         |Ano         |Ano         |
|Vlastní vlastnosti zprávy     |Ano         |Ne         |Ne         |
|Vstupy a výstupy sdílet mezi více dotazů     |Ne         |Ano         |Ano         |
|UDF JavaScriptu a UDA     |Ano         |Ano         |Pouze Windows         |
|Machine Learning popisky     |Ano, ale dotaz nelze provést test        |Ano, ale nelze provést test místně         |Ne         |
|Úroveň kompatibility     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|Integrované funkce pro detekci anomálií na základě ML     |Ano         |Ano         |Ano         |
|Integrované geoprostorové funkce     |Ano         |Ano         |Ano         |
|Testování dotazu s ukázkový soubor     |Ano         |Ano         |Ano         |
|Živé testování místní data     |Ne         |Ano         |Ne         |
|Zobrazení seznamu úloh a zobrazení úlohy entity     |Ano         |Ano         |Ano         |
|Exportovat úlohy do místní projekt     |Ne         |Ano         |Ano         |
|Odeslání, spuštění a zastavení úloh     |Ano         |Ano         |Ano         |
|Správy zdrojového kódu     |Ne         |Ano         |Ano         |
|Podporu CI/CD     |Částečně         |Ano         |Ano         |
|Zobrazit úlohy metriky a diagram     |Ano         |Ano         |Otevřít na portálu         |
|Zobrazit chyby za běhu úlohy     |Ano         |Ano         |Ne         |
|Diagnostické protokoly     |Ano         |Ne         |Ne         |


## <a name="iot-edge-job-features"></a>Funkce úlohy IoT Edge

|Funkce  |Portál  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Úlohy pro tvorbu     |Ano         |Ano         |Ne         |
|Správy zdrojového kódu     |Ne         |Ano         |Ne         |
|Exportovat úlohy do místní projekt     |Ne         |Ano         |Ne         |
|Testování dotazu s ukázkový soubor     |Ano         |Ano         |Ne         |
|Vstupy a výstupy sdílet mezi více dotazů     |Ne         |Ano         |Ne         |
|C# UDF     |Ne         |Ano         |Ne         |
|Odeslání, spuštění a zastavení úloh     |Ano         |Ano         |Ne         |
|Zobrazení seznamu úloh a zobrazení úlohy entity     |Ano         |Ano         |Ne         |
|Zobrazit úlohy metriky a diagram     |Ano         |Částečně         |Ne         |
|Zobrazit chyby za běhu úlohy     |Ano         |Částečně         |Ne         |
|Podporu CI/CD     |Ne         |Ne         |Ne         |


## <a name="next-steps"></a>Další postup

* [Azure Stream Analytics na hraničních zařízeních IoT](stream-analytics-edge.md)
* [Kurz: Zápis C# uživatelem definovanou funkci pro úlohu Azure Stream Analytics IoT Edge (Preview)](stream-analytics-edge-csharp-udf.md)
* [Vývoj úloh Stream Analytics IoT Edge pomocí nástrojů sady Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Chcete-li zobrazit úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-vs-tools.md)
* [Prozkoumejte službu Azure Stream Analytics pomocí sady Visual Studio Code (Preview)](vscode-explore-jobs.md)


