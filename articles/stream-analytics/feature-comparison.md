---
title: Porovnání funkcí Azure Stream Analytics
description: Tento článek porovnává funkce podporované pro cloud Azure Stream Analytics a úlohy IoT Edge na webu Azure Portal, Visual Studio a visual studio kódu.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: d3b27a50fa86916b71c84b30ecdbf45deb0ec45c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770756"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Porovnání funkcí Azure Stream Analytics

Pomocí Azure Stream Analytics můžete vytvářet streamovací řešení v cloudu a na IoT Edge pomocí [portálu Azure](stream-analytics-quick-create-portal.md)Portal , [Visual Studia](stream-analytics-quick-create-vs.md)a kódu [Visual Studia](quick-create-vs-code.md). Tabulky v tomto článku ukazují, které funkce jsou podporovány každou platformou pro oba typy úloh.

> [!NOTE]
> Nástroje Visual Studio a Visual Studio Code nepodporují úlohy v oblastech Čína – východ, Čína – sever, Německo – střed a Německo – severovýchod.

## <a name="cloud-job-features"></a>Cloudové funkce úloh


|Funkce  |Portál  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Příčná platforma     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Vytváření skriptů     |Ano         |Ano         |Ano         |
|Skript Intellisense     |Zvýraznění syntaxe         |Zvýraznění syntaxe</br>Dokončení kódu</br>Značka chyby         |Zvýraznění syntaxe</br>Dokončení kódu</br>Značka chyby         |
|Definování všech typů vstupů, výstupů a konfigurací úloh     |Ano         |Ano         |Ano         |
|Správa zdrojového kódu     |Ne         |Ano         |Ano         |
|Podpora CI/CD     |Částečné         |Ano         |Ano         |
|Sdílení vstupů a výstupů ve více dotazech     |Ne         |Ano         |Ano         |
|Testování dotazů pomocí ukázkového souboru     |Ano         |Ano        |Ano         |
|Místní testování živých dat     |Ne         |Ano       |Ano      |
|Zobrazit seznam úloh a zobrazit entity úlohy     |Ano         |Ano        |Ano         |
|Export úlohy do místního projektu     |Ne         |Ano         |Ano         |
|Odeslání, zahájení a zastavení úloh     |Ano         |Ano         |Ano         |
|Zobrazit metriky úloh a diagram     |Ano         |Ano         |Otevřít na portálu         |
|Zobrazit chyby běhu za běhu úlohy     |Ano         |Ano         |Ne         |
|Diagnostické protokoly     |Ano         |Ne         |Ne         |
|Vlastní vlastnosti zprávy     |Ano         |Ano         |Ne       |
|C# vlastní kód funkce a deserializer|Režim jen pro čtení|Ano|Ne|
|JavaScript UDF a UDA     |Ano         |Ano         |Jen ve Windows         |
|Služba Machine Learning     |Ano        |Ano         |Ne         |
|Machine Learning Studio     |Ano, ale dotaz nelze otestovat.        |Ano |Ne         |
|Úroveň kompatibility     |1.0</br>1.1</br>1.2 (výchozí)         |1.0</br>1.1</br>1.2 (výchozí)           |1.0</br>1.1</br>1.2 (výchozí)           |
|Vestavěné funkce detekce anomálií založených na ml     |Ano         |Ano         |Ano         |
|Vestavěné geoprostorové funkce     |Ano         |Ano         |Ano         |



## <a name="iot-edge-job-features"></a>Funkce úloh IoT Edge

|Funkce  |Portál  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Vytváření úloh     |Ano         |Ano         |Ne         |
|Správa zdrojového kódu     |Ne         |Ano         |Ne         |
|Export úlohy do místního projektu     |Ne         |Ano         |Ne         |
|Testování dotazů pomocí ukázkového souboru     |Ano         |Ano         |Ne         |
|Sdílení vstupů a výstupů ve více dotazech     |Ne         |Ano         |Ne         |
|Uživatelsky definovaná funkce v C#     |Ne         |Ano         |Ne         |
|Odeslat úlohy     |Ano         |Ano         |Ne         |
|Zobrazit seznam úloh a zobrazit entity úlohy     |Ano         |Ano         |Ne         |
|Zobrazit metriky úloh a diagram     |Ano         |Částečné         |Ne         |
|Zobrazit chyby běhu za běhu úlohy     |Ano         |Částečné         |Ne         |
|Podpora CI/CD     |Ne         |Ne         |Ne         |


## <a name="next-steps"></a>Další kroky

* [Azure Stream Analytics na hraničních zařízeních IoT](stream-analytics-edge.md)
* [Kurz: Napište uživatelem definovanou funkci C# pro úlohu Azure Stream Analytics IoT Edge (preview)](stream-analytics-edge-csharp-udf.md)
* [Vývoj úloh IoT Edge služby Stream Analytics pomocí nástrojů Visual Studia](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Zobrazení úloh Azure Stream Analytics pomocí Visual Studia](stream-analytics-vs-tools.md)
* [Prozkoumejte Azure Stream Analytics pomocí kódu Visual Studia (preview)](visual-studio-code-explore-jobs.md)


