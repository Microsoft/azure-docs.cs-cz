---
title: Získání dat a principy fázi životní cyklus vědeckého zpracování týmových dat – Azure | Dokumentace Microsoftu
description: Cíle, úkoly a úkoly pro získání dat a principy fázi vašich projektů datové vědy
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 13cbcead381257c87f17dc8ed6666a1b8964d81a
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441568"
---
# <a name="data-acquisition-and-understanding"></a>Získávání a pochopení dat

Tento článek popisuje cíle, úkoly a úkoly, které jsou přidružené k získání dat a principy fáze nástroje zpracování týmových dat vědy (TDSP). Tento proces zajišťuje doporučené životní cyklus, který vám pomůže strukturovat vaše projekty pro datové vědy. Životní cyklus popisuje hlavní fáze, které jsou projekty obvykle spouštěny, často opakované:

   1. **Principy podniku**
   2. **Získávání a pochopení dat**
   3. **Modelování**
   4. **Nasazení**
   5. **Schválení zákazníkem**

Zde je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cíle
* Vytvořit čisté, vysoce kvalitní datové sady, jehož vztah k proměnné cíl je srozumitelný. Vyhledejte sadu dat prostředí odpovídající analytics, jste připraveni k modelu.
* Vývoj architektury řešení z datového kanálu, který se aktualizuje a pravidelně získává data.

## <a name="how-to-do-it"></a>Jak na to
Existují tři hlavní úkoly zákazníky a vyřešené v této fázi:

   * **Zpracování příjmu dat** do cílového analytických prostředí.
   * **Prozkoumejte data** k určení, zda je kvalita dat odpovídající odpověď na otázku. 
   * **Nastavení datového kanálu** ke stanovení skóre pro nové nebo pravidelně aktualizují data.

### <a name="ingest-the-data"></a>Zpracování příjmu dat
Nastavení procesu přesunu dat ze zdrojového umístění do cílového umístění, ve kterém spouštíte operací pro analýzy, jako je trénování a predikcí. Technické podrobnosti a možnosti, jak přesunout data s využitím různých datových služeb Azure najdete v tématu [načtení dat do prostředí úložiště pro analýzu](ingest-data.md). 

### <a name="explore-the-data"></a>Zkoumání dat
Předtím, než jste trénování modelů, potřebujete pro vývoj zvukové objasnit význam dat. Skutečné datové sady jsou často hlučného, chybí hodnoty nebo mají celou řadu dalších rozdíly. Shrnutí dat a vizualizaci můžete použít k auditu kvality dat a zadejte informace potřebné ke zpracování dat, než bude připravená pro modelování. Tento proces je často iterativní.

TDSP poskytuje automatizované nástroje, volá [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), k vizualizaci dat a připravit data souhrnné sestavy. Doporučujeme začít s IDEAR, abyste mohli zkoumat data, která vám pomůžou s vývojem počáteční data pochopit interaktivně bez kódování. Můžete napsat vlastní kód pro zkoumání a vizualizace. Pokyny k čištění dat, naleznete v tématu [úlohy k přípravě dat pro rozšířené strojové učení](prepare-data.md).  

Jakmile budete spokojeni s kvalitou očištěnou data, dalším krokem je pro lepší pochopení vzorů, které jsou obsaženy v datech. To vám pomůže vybrat a vývoj odpovídající prediktivní model pro váš cíl. Hledat důkazy pro jak řádně připojených dat je do cíle. Zjistěte, zda je dostatečným množstvím dat pro přesun s dalšími kroky modelování. Tento proces je opět často iterativní. Můžete potřebovat najít nové zdroje dat s daty přesnější nebo relevantnější pro rozšíření sady dat původně identifikované v předchozím kroku. 

### <a name="set-up-a-data-pipeline"></a>Nastavení datového kanálu
Kromě počáteční ingestování a čištění dat obvykle musíte nastavit procesu skóre pro nová data nebo aktualizaci dat pravidelně jako součást probíhající studijní postup. To provedete nastavením datového kanálu nebo pracovního postupu. [Přesunout data z místní instance systému SQL Server do Azure SQL Database pomocí služby Azure Data Factory](move-sql-azure-adf.md) článek obsahuje příklad toho, jak vytvořit kanál s [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

V této fázi se při vývoji architektury řešení z datového kanálu. Při vývoji kanál zároveň s další fází projektu datové vědy. V závislosti na vašich obchodních potřeb a omezení vaše stávající systémy, ve které je integrované toto řešení kanálu může být jeden z následujících akcí: 

   * Na základě služby batch
   * Streamování nebo v reálném čase 
   * Hybridní 

## <a name="artifacts"></a>Artefakty
Tady jsou výstupy v této fázi:

   * [Sestava kvality dat](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): Tato sestava obsahuje shrnutí dat, vztahy mezi všechny atributy a cíl, proměnné pořadí a další. [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) nástroje, které jsou k dispozici jako součást TDSP můžete rychle vygenerovat tuto sestavu na všechny tabulkové datové sady, jako je například soubor CSV nebo relační tabulky. 
   * **Architektura řešení**: architekturu řešení může být diagram nebo popis vašeho datového kanálu, který jste k provedení vyhodnocení neboli predikcím na nová data po vytvoření modelu. Obsahuje také kanál tak, aby přeučování váš model založený na nová data. Store v dokumentu [projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) adresáře, když použijete šablonu struktury TDSP adresáře.
   * **Kontrolní bod rozhodnutí**: před zahájením vytváření plně funkcí a vytváření modelů, prezentovaný projektu určuje, jestli je očekávána hodnota dostatečná k pokračování usilovat o normalizaci ho. Například může být připraveni pokračovat, musíte shromáždit více dat, nebo opustit projektu, protože data neexistuje odpověď na otázku.

## <a name="next-steps"></a>Další postup

Tady jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a pochopení dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Schválení zákazníkem](lifecycle-acceptance.md)

Poskytujeme úplného začátku do konce návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. [Příklad návody](walkthroughs.md) článek obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

Příklady toho, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio najdete v tématu [TDSP pomocí služby Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).
