---
title: Získávání dat a porozumění vědeckému zpracování týmových dat
description: Cíle, úkoly a dodávky pro získávání dat a porozumění fázích vašich projektů pro datové vědy
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 85d7159edb1bc8608165264578d77519bf09efc2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84195066"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Získávání dat a porozumění fázi vědeckého zpracování týmových dat

Tento článek popisuje cíle, úlohy a dodávky, které jsou spojené s získáváním dat a porozumění fázím procesu vědeckého zpracování týmových dat (TDSP). Tento proces poskytuje doporučený životní cyklus, který můžete použít ke strukturování projektů pro vědu. Životní cyklus popisuje hlavní fáze, které se obvykle spouštějí projekty, často iterativní:

   1. **Principy podniku**
   2. **Získávání a pochopení dat**
   3. **Modelování**
   4. **Nasazení**
   5. **Přijetí zákazníky**

Tady je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cíle
* Vytvoří čistou a vysoce kvalitní datovou sadu, jejíž relace s cílovými proměnnými je srozumitelná. Vyhledejte datovou sadu v příslušném analytickém prostředí, abyste byli připraveni na model.
* Vytvořte architekturu řešení datového kanálu, která aktualizuje data a pravidelně je zarovnává.

## <a name="how-to-do-it"></a>Jak to provést
V této fázi jsou řešeny tři hlavní úlohy:

   * Ingestujte **data** do cílového analytického prostředí.
   * **Prozkoumejte data** a zjistěte, jestli je kvalita dat dostačující pro odpověď na otázku. 
   * **Nastavení datového kanálu** pro hodnocení nových nebo pravidelně aktualizovaných dat

### <a name="ingest-the-data"></a>Ingestování dat
Nastavte proces přesunu dat ze zdrojových umístění do cílových umístění, kde spouštíte analytické operace, jako je například školení a předpovědi. Technické podrobnosti a možnosti, jak přesunout data s různými datovými službami Azure, najdete v tématu [načtení dat do prostředí úložiště pro analýzu](ingest-data.md). 

### <a name="explore-the-data"></a>Prozkoumání dat
Než začnete vytvářet své modely, je nutné vyvinout dobré znalosti dat. Reálné datové sady jsou často odolné proti chybám, neexistují žádné hodnoty nebo mají hostitele jiné nesrovnalosti. Souhrn a vizualizace dat můžete použít k auditování kvality vašich dat a poskytnutí informací, které potřebujete ke zpracování dat, než bude připravená k modelování. Tento proces je často iterativní.

TDSP poskytuje automatizovaný nástroj označovaný jako [myšlenku](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), který vám umožní vizualizovat data a připravit sestavy souhrnu dat. Doporučujeme nejprve začít s nástrojem myšlenku a prozkoumat data, která vám pomůžou při interaktivním vývoji počátečních dat bez kódování. Pak můžete napsat vlastní kód pro zkoumání a vizualizaci dat. Pokyny k čištění dat najdete v tématu [úlohy pro přípravu dat pro rozšířené strojové učení](prepare-data.md).  

Až budete spokojení s kvalitou vyčištěných dat, je dalším krokem lepší porozumět vzorům, které jsou v těchto datech obsaženy. Tato analýza dat vám pomůže vybrat a vyvinout vhodný prediktivní model pro váš cíl. Vyhledejte důkaz, jak dobře připojená data jsou k cíli. Pak určete, zda je k dispozici dostatek dat pro přesun vpřed s dalšími kroky modelování. Tento proces je znovu často iterativní. Možná budete muset najít nové zdroje dat s přesnější nebo důležitějšími daty, abyste mohli rozšířit sadu dat původně identifikovanou v předchozí fázi. 

### <a name="set-up-a-data-pipeline"></a>Nastavení datového kanálu
Kromě počátečního příjmu a čištění dat je obvykle potřeba nastavit proces, který bude vyhodnocovat nová data nebo pravidelně aktualizovat data v rámci probíhajícího výukového procesu. Bodování může být dokončeno s datovým kanálem nebo pracovním postupem. [Přesunutí dat z instance SQL Server do Azure SQL Database pomocí Azure Data Factory](move-sql-azure-adf.md) článku obsahuje příklad nastavení kanálu s [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

V této fázi vyvíjíte architekturu řešení datového kanálu. Kanál vyvíjíme paralelně s další fází projektu pro datové vědy. V závislosti na potřebách vaší firmy a omezeních stávajících systémů, do kterých je toto řešení integrováno, může kanál představovat jednu z následujících možností: 

   * Založený na dávce
   * Streamování nebo reálný čas 
   * Hybridní 

## <a name="artifacts"></a>Artifacts
V této fázi jsou následující dodávky:

   * [Sestava kvality dat](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): Tato sestava obsahuje souhrny dat, vztahy mezi jednotlivými atributy a cíli, hodnocení proměnných a další. Nástroj [designu](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) , který je součástí TDSP, může rychle vygenerovat tuto sestavu na jakékoli tabulkové datové sadě, jako je třeba soubor CSV nebo relační tabulka. 
   * **Architektura řešení**: Architektura řešení může být diagramem nebo popisem datového kanálu, který používáte ke spuštění bodování nebo předpovědi s novými daty po vytvoření modelu. Obsahuje také kanál, který umožňuje přeškolit model na základě nových dat. Uložte dokument do adresáře [projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) , když použijete šablonu struktury adresáře TDSP.
   * **Rozhodnutí kontrolního bodu**: než začnete pracovat s funkcemi a vytvářením modelů, můžete projekt znovu vyhodnotit, abyste zjistili, jestli je očekávaná hodnota dostačující pro pokračování v provádění. Můžete být například připravení pokračovat, potřebovat shromažďovat další data nebo opustit projekt, protože data neexistují pro zodpovězení otázky.

## <a name="next-steps"></a>Další kroky

Tady jsou odkazy na jednotlivé kroky v životním cyklu TDSP:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a pochopení dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníky](lifecycle-acceptance.md)

Poskytujeme kompletní návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. Článek [příklad návodů](walkthroughs.md) obsahuje seznam scénářů s odkazy a popisy miniatur. Návody ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu a vytvořit tak inteligentní aplikaci. 

Příklady, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio, naleznete v tématu [použití TDSP s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).
