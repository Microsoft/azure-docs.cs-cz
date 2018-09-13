---
title: Průvodce pro službu Azure Machine Learning zdroje dat Azure | Dokumentace Microsoftu
description: Vysvětluje v Průvodci zdroje dat z aplikace workbench AML
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: c74229504a43179673cc99ccff321b65e3f6ed4f
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644483"
---
# <a name="data-source-wizard"></a>Průvodce zdroje dat #

Průvodce zdrojem dat je rychlý a snadný způsob, jak převést datové sady do aplikace Azure ML Workbench bez kódu. To je, kde můžete zvolit také strategii ukázkové datové sady a datové typy pro každý sloupec. 

## <a name="step-1-trigger-the-data-source-wizard"></a>Krok 1: Spuštění Průvodce zdrojem dat ## 

Chcete-li přenést data do projektu s použitím Průvodce zdrojem dat. Vyberte **+** tlačítko vedle do vyhledávacího pole v zobrazení dat a zvolte Přidat zdroj dat. 

![Přidat zdroj dat](media/data-source-wizard/add-data-source.png)

## <a name="step-2-select-where-data-is-stored"></a>Krok 2: Vyberte, kde jsou uložená data ##
Nejprve určete, jak se aktuálně ve vaše data. By mohl být uložen v plochého souboru nebo adresáře, do souboru parquet, Excelový soubor nebo databáze. Další informace najdete v tématu [podporované zdroje dat](data-prep-appendix2-supported-data-sources.md).

![Krok 1](media/data-source-wizard/step1.png)

## <a name="step-3-select-data-file"></a>Krok 3: Vyberte soubor dat ##
Pro soubor nebo adresář zadejte cestu k souboru. Z rozevíracího seznamu vyberte umístění dat – to může být místní cesta k souboru nebo úložiště objektů Blob v Azure. 

Zadejte cestu podle jejího psaní nebo kliknutím na **Procházet...** tlačítko a vyhledejte ho. Můžete procházet adresář nebo jeden nebo více souborů.

Klikněte na tlačítko **Dokončit** přijměte výchozí hodnoty pro zbývající kroky nebo dále pokračovat k dalšímu kroku.


![Krok 4](media/data-source-wizard/step2.png)

## <a name="step-4-choose-file-parameters"></a>Krok 4: Výběr souboru parametrů ##

Průvodce zdrojem dat můžete tento soubor automaticky rozpoznat typ oddělovače a kódování. Příklad dat bude vypadat se také zobrazí. Můžete také změnit některý z těchto parametrů ručně. 

![Krok 5](media/data-source-wizard/step3.png)

## <a name="step-5-set-data-types-for-columns"></a>Krok 5: Nastavení datových typů pro sloupce ##

Průvodce zdrojem dat automaticky rozpozná datové typy sloupců datové sadě. Pokud ho přístupů do jedné nebo chcete vynutit datový typ, můžete ručně změnit datový typ. **UKÁZKOVÝ výstup dat** sloupci se zobrazuje příklady, jak data vypadat.

Pro sloupce, které odvodí přípravy dat, obsahující data můžete být vyzváni k výběru pořadí měsíc a den ve formátu data. Například může představovat 2. ledna 1/2/2013 (v tomto případě vyberte *den měsíce*) nebo 1. února (vyberte *měsíc a den*).

![Krok 6](media/data-source-wizard/step4.png)

## <a name="step-6-choose-sampling-strategy-for-data"></a>Krok 6: Zvolte strategie vzorkování dat ##

Můžete zadat jeden nebo více strategie vzorkování pro datovou sadu a zvolte jednu z nich jako aktivní strategie. Ve výchozím nastavení je načíst řádky Top 10000. Tento příklad můžete upravit kliknutím na **upravit** tlačítko na panelu nástrojů nebo přidání nové strategie kliknutím na + nový. Strategie, které jsou v současné době podporují

-     Nejvyšší počet řádků
-     Náhodná čísla řádků
-     Náhodné procentuální podíl řádků
-     Úplný soubor

Můžete zadat libovolný počet vzorkování strategie, jak chcete, ale existuje pouze jeden, který může být nastavena na aktivní, když se připravují se vaše data. Můžete nastavit každé strategie na aktivní tak, že vyberete strategie a kliknutím na nastavit jako aktivní na panelu nástrojů.

V závislosti na tom, odkud data pocházejí z nemusí být některé strategie vzorku podporované. Další informace o vzorkování, podívejte se na část vzorkování v [tento dokument](data-prep-user-guide.md) 

![Krok 6](media/data-source-wizard/step5.png)

## <a name="step-7-path-column-handling"></a>Krok 7: Cesta sloupec zpracování ##

Pokud cesta k souboru obsahuje důležitá data, můžete zahrnout jako první sloupec v datové sadě. Tato možnost bude užitečné, pokud jsou uvedení ve více souborech. V opačném případě je možné ho.

![Krok 7](media/data-source-wizard/step6.png)

Po kliknutí na Dokončit se nový zdroj dat se přidají do projektu. Najdete ho ve skupině zdrojů dat v zobrazení dat, nebo jako soubor dsource **zobrazení souboru**.
