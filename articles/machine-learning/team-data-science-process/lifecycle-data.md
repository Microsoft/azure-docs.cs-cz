---
title: Získávání a pochopení dat vědecké zpracování týmových dat
description: Cíle, úkoly a úkoly pro získání dat a principy fázi vašich projektů datové vědy
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
ms.openlocfilehash: 3c299e9ec42d63812804b5ff7e50324a2de94200
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720499"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Získání dat a principy fázi vědecké zpracování týmových dat

Tento článek popisuje cíle, úkoly a úkoly, které jsou přidružené k získání dat a principy fáze nástroje zpracování týmových dat vědy (TDSP). Tento proces zajišťuje doporučené životní cyklus, který vám pomůže strukturovat vaše projekty pro datové vědy. Životní cyklus popisuje hlavní fáze, které jsou projekty obvykle spouštěny, často opakované:

   1. **Obchodní porozumění**
   2. **Získávání a porozumění dat**
   3. **Situací**
   4. **Nasazení**
   5. **Přijetí zákazníkovi**

Zde je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cíle
* Vytvořit čisté, vysoce kvalitní datové sady, jehož vztah k proměnné cíl je srozumitelný. Vyhledejte sadu dat prostředí odpovídající analytics, jste připraveni k modelu.
* Vývoj architektury řešení z datového kanálu, který se aktualizuje a pravidelně získává data.

## <a name="how-to-do-it"></a>Jak na to
Existují tři hlavní úkoly zákazníky a vyřešené v této fázi:

   * Ingestujte **data** do cílového analytického prostředí.
   * **Prozkoumejte data** a zjistěte, jestli je kvalita dat dostačující pro odpověď na otázku. 
   * **Nastavení datového kanálu** pro hodnocení nových nebo pravidelně aktualizovaných dat

### <a name="ingest-the-data"></a>Zpracování příjmu dat
Nastavení procesu přesunu dat ze zdrojového umístění do cílového umístění, ve kterém spouštíte operací pro analýzy, jako je trénování a predikcí. Technické podrobnosti a možnosti, jak přesunout data s různými datovými službami Azure, najdete v tématu [načtení dat do prostředí úložiště pro analýzu](ingest-data.md). 

### <a name="explore-the-data"></a>Zkoumání dat
Předtím, než jste trénování modelů, potřebujete pro vývoj zvukové objasnit význam dat. Skutečné datové sady jsou často hlučného, chybí hodnoty nebo mají celou řadu dalších rozdíly. Shrnutí dat a vizualizaci můžete použít k auditu kvality dat a zadejte informace potřebné ke zpracování dat, než bude připravená pro modelování. Tento proces je často iterativní.

TDSP poskytuje automatizovaný nástroj označovaný jako [myšlenku](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), který vám umožní vizualizovat data a připravit sestavy souhrnu dat. Doporučujeme začít s IDEAR, abyste mohli zkoumat data, která vám pomůžou s vývojem počáteční data pochopit interaktivně bez kódování. Můžete napsat vlastní kód pro zkoumání a vizualizace. Pokyny k čištění dat najdete v tématu [úlohy pro přípravu dat pro rozšířené strojové učení](prepare-data.md).  

Jakmile budete spokojeni s kvalitou očištěnou data, dalším krokem je pro lepší pochopení vzorů, které jsou obsaženy v datech. Tato analýza dat vám pomůže vybrat a vyvinout vhodný prediktivní model pro váš cíl. Hledat důkazy pro jak řádně připojených dat je do cíle. Zjistěte, zda je dostatečným množstvím dat pro přesun s dalšími kroky modelování. Tento proces je opět často iterativní. Můžete potřebovat najít nové zdroje dat s daty přesnější nebo relevantnější pro rozšíření sady dat původně identifikované v předchozím kroku. 

### <a name="set-up-a-data-pipeline"></a>Nastavení datového kanálu
Kromě počáteční ingestování a čištění dat obvykle musíte nastavit procesu skóre pro nová data nebo aktualizaci dat pravidelně jako součást probíhající studijní postup. Bodování může být dokončeno s datovým kanálem nebo pracovním postupem. [Přesunutí dat z místní instance SQL Server do Azure SQL Database s Azure Data Factory](move-sql-azure-adf.md) článkem nabízí příklad nastavení kanálu s [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

V této fázi se při vývoji architektury řešení z datového kanálu. Při vývoji kanál zároveň s další fází projektu datové vědy. V závislosti na potřebách vaší firmy a omezeních stávajících systémů, do kterých je toto řešení integrováno, může kanál představovat jednu z následujících možností: 

   * Na základě služby batch
   * Streamování nebo v reálném čase 
   * Hybridní 

## <a name="artifacts"></a>Artefakty
Tady jsou výstupy v této fázi:

   * [Sestava kvality dat](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): Tato sestava obsahuje souhrny dat, vztahy mezi jednotlivými atributy a cíli, hodnocení proměnných a další. Nástroj [designu](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) , který je součástí TDSP, může rychle vygenerovat tuto sestavu na jakékoli tabulkové datové sadě, jako je třeba soubor CSV nebo relační tabulka. 
   * **Architektura řešení**: Architektura řešení může být diagramem nebo popisem datového kanálu, který používáte ke spuštění bodování nebo předpovědi s novými daty po vytvoření modelu. Obsahuje také kanál tak, aby přeučování váš model založený na nová data. Uložte dokument do adresáře [projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) , když použijete šablonu struktury adresáře TDSP.
   * **Rozhodnutí kontrolního bodu**: než začnete pracovat s funkcemi a vytvářením modelů, můžete projekt znovu vyhodnotit, abyste zjistili, jestli je očekávaná hodnota dostačující pro pokračování v provádění. Například může být připraveni pokračovat, musíte shromáždit více dat, nebo opustit projektu, protože data neexistuje odpověď na otázku.

## <a name="next-steps"></a>Další kroky

Tady jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Obchodní porozumění](lifecycle-business-understanding.md)
   2. [Získávání a porozumění dat](lifecycle-data.md)
   3. [Situací](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníkovi](lifecycle-acceptance.md)

Poskytujeme kompletní návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. Článek [příklad návodů](walkthroughs.md) obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

Příklady, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio, naleznete v tématu [použití TDSP s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).
