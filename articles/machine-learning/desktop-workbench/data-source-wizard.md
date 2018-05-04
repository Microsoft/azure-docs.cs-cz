---
title: Zdroj dat Azure, Průvodce pro Azure Machine Learning | Microsoft Docs
description: Popisuje nástroje AML workbench Průvodce zdrojem dat
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: 4f9a2249142220f3c57cb2e479a175d4227749fd
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="data-source-wizard"></a>Průvodce zdrojem dat #

Průvodce zdrojem dat je rychlý a snadný způsob, jak přizpůsobit datovou sadu Azure ML Workbench bez kódu. Je, kde můžete také vybrat strategie ukázka pro datovou sadu a datové typy pro každý sloupec. 

## <a name="step-1-trigger-the-data-source-wizard"></a>Krok 1: Spuštění Průvodce zdrojem dat ## 

K přenosu dat do projektu pomocí Průvodce zdrojem dat. Vyberte **+** tlačítko vedle pole hledání v zobrazení dat a vyberte zdroj dat přidat. 

![Přidat zdroje dat](media/data-source-wizard/add-data-source.png)

## <a name="step-2-select-where-data-is-stored"></a>Krok 2: Vyberte, které jsou uložená data ##
Nejprve zadejte, jak je aktuálně v vaše data. Může být uložený v plochý soubor nebo adresář, parquet souboru, soubor aplikace Excel nebo databáze. Další informace najdete v tématu [podporované zdroje dat](data-prep-appendix2-supported-data-sources.md).

![Krok 1](media/data-source-wizard/step1.png)

## <a name="step-3-select-data-file"></a>Krok 3: Vyberte soubor dat ##
Pro soubor nebo adresář zadejte cestu k souboru. Z rozevíracího seznamu vyberte umístění dat – to může být místní cesta k souboru nebo Azure Blob Storage. 

Zadejte cestu v zadáním nebo kliknutím na **Procházet...** tlačítko procházení pro ni. Můžete procházet adresář, nebo jeden nebo více souborů.

Klikněte na tlačítko **Dokončit** přijměte výchozí hodnoty pro zbytek kroky nebo další pokračovat k dalšímu kroku.


![Krok 4](media/data-source-wizard/step2.png)

## <a name="step-4-choose-file-parameters"></a>Krok 4: Zvolte soubor parametrů ##

Průvodce zdrojem dat může automaticky zjistit soubor typu, oddělovače a kódování. Zobrazí se také příklad vzhled data. Můžete také některý z těchto parametrů ručně změnit. 

![Krok 5](media/data-source-wizard/step3.png)

## <a name="step-5-set-data-types-for-columns"></a>Krok 5: Nastavení datových typů pro sloupce ##

Průvodce zdrojem dat automaticky rozpozná datové typy sloupců datové sadě. Pokud ho neúspěšných přístupů do jednoho nebo pokud chcete vynutit datový typ, můžete ručně změnit datový typ. **UKÁZKOVÁ výstupní DATA** sloupci se zobrazuje příklady, jak vypadají data.

Pro sloupce, které odvodí, že příprava dat tak, aby obsahovala data zobrazí se výzva k výběru pořadí měsíce a dne ve formátu data. Například by mohl představovat 2. ledna 1/2/2013 (v takovém případě vyberte *den měsíce*) nebo 1. února (vyberte *den měsíce*).

![Krok 6](media/data-source-wizard/step4.png)

## <a name="step-6-choose-sampling-strategy-for-data"></a>Krok 6: Zvolte vzorkování strategii pro data ##

Můžete zadat jeden nebo více strategií vzorkování pro datovou sadu a zvolte jednu jako aktivní strategie. Výchozí hodnota je načíst řádků Top 10000. Tato ukázka můžete upravit kliknutím na **upravit** v panelu nástrojů na tlačítko nebo přidejte nové strategie kliknutím na + nový. Jsou strategií, které jsou v současné době podporují

-     Nejvyšší počet řádků
-     Náhodná čísla řádků
-     Náhodné podíl řádků
-     Celého souboru

Můžete zadat, ale existuje pouze jeden, můžete nastavit jako aktivní při přípravě data výběrové strategie. Můžete nastavit jakékoli strategie jako aktivní strategie výběrem a kliknutím na nastavit jako aktivní v panelu nástrojů.

V závislosti na tom, odkud pochází data nemusí být některé ukázkové strategií podporována. Další informace o vzorkování, podívejte se na části vzorkování v [tento dokument](data-prep-user-guide.md) 

![Krok 6](media/data-source-wizard/step5.png)

## <a name="step-7-path-column-handling"></a>Krok 7: Cesta sloupec zpracování ##

Pokud cesta k souboru obsahuje důležitá data, můžete ho zahrňte jako první sloupec v datové sadě. Tato možnost by být užitečné, pokud jsou uvedení ve více souborech. Můžete, jinak nebudou zahrnuty.

![Krok 7](media/data-source-wizard/step6.png)

Po kliknutí na tlačítko Dokončit, nový zdroj dat přidá do projektu. Najdete ho ve skupině zdrojů dat v zobrazení dat, nebo jako soubor dsource v **zobrazení souboru**.
