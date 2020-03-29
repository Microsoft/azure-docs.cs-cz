---
title: Získávání dat a pochopení procesu týmové datové vědy
description: Cíle, úkoly a výstupy pro fázi získávání dat a pochopení vašich projektů datových věd
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720499"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Fáze získávání a pochopení dat procesu teamových datových věd

Tento článek popisuje cíle, úkoly a dodávky spojené s získávání dat a pochopení fáze procesu vědecké ho týmu dat (TDSP). Tento proces poskytuje doporučený životní cyklus, který můžete použít ke strukturování projektů datové vědy. Životní cyklus popisuje hlavní fáze, které projekty obvykle provádějí, často iterativně:

   1. **Principy podniku**
   2. **Získávání a pochopení dat**
   3. **Modelování**
   4. **Nasazení**
   5. **Přijetí zákazníky**

Zde je vizuální znázornění životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cíle
* Vytvoří čistou, vysoce kvalitní datovou sadu, jejíž vztah k cílovým proměnným je chápán. Vyhledejte sadu dat v příslušném analytickém prostředí, abyste byli připraveni k modelování.
* Vytvořte architekturu řešení datového kanálu, která data pravidelně aktualizuje a skóruje.

## <a name="how-to-do-it"></a>Jak na to
V této fázi jsou řešeny tři hlavní úkoly:

   * **Ingestujte data** do cílového analytického prostředí.
   * **Prozkoumejte data** a zjistěte, zda je kvalita dat dostatečná pro odpověď na otázku. 
   * **Nastavte datový kanál pro** skóre nových nebo pravidelně aktualizovaných dat.

### <a name="ingest-the-data"></a>Ingestování dat
Nastavte proces přesunout data ze zdrojových umístění do cílových umístění, kde spustíte analytické operace, jako je školení a předpovědi. Technické podrobnosti a možnosti, jak přesunout data pomocí různých datových služeb Azure, najdete v [tématu Načítání dat do prostředí úložiště pro analýzy](ingest-data.md). 

### <a name="explore-the-data"></a>Zkoumání dat
Než budete trénovat své modely, musíte rozvíjet správné pochopení dat. Datové sady v reálném světě jsou často hlučné, chybí hodnoty nebo mají řadu dalších nesrovnalostí. Pomocí souhrnu dat a vizualizace můžete auditovat kvalitu dat a poskytnout informace potřebné ke zpracování dat, než budou připravena k modelování. Tento proces je často iterativní.

TDSP poskytuje automatizovaný nástroj s názvem [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), který pomáhá vizualizovat data a připravit souhrnné sestavy dat. Doporučujeme začít s IDEAR nejprve prozkoumat data pomoci rozvíjet počáteční porozumění dat interaktivně bez kódování. Pak můžete napsat vlastní kód pro zkoumání dat a vizualizaci. Pokyny k čištění dat najdete v [tématu Úkoly připravit data pro rozšířené strojové učení](prepare-data.md).  

Poté, co jste spokojeni s kvalitou vyčištěných dat, dalším krokem je lépe pochopit vzory, které jsou vlastní datům. Tato analýza dat vám pomůže vybrat a vyvinout vhodný prediktivní model pro váš cíl. Podívejte se na důkazy o tom, jak dobře jsou data připojena k cíli. Pak určete, zda je dostatek dat pro posun vpřed s další kroky modelování. Opět platí, že tento proces je často iterativní. Možná budete muset najít nové zdroje dat s přesnějšími nebo relevantnějšími údaji, abyste rozšířili sadu dat původně identifikovanou v předchozí fázi. 

### <a name="set-up-a-data-pipeline"></a>Nastavení datového kanálu
Kromě počátečního ingestování a čištění dat je obvykle nutné nastavit proces pro shromažďování nových dat nebo pravidelnou aktualizaci dat jako součást probíhajícího procesu učení. Vyhodnocování může být dokončeno datovým kanálem nebo pracovním postupem. Přesunout [data z místní instance SQL Serveru do Azure SQL Database s Azure Data Factory](move-sql-azure-adf.md) článek poskytuje příklad, jak nastavit kanál s Azure Data [Factory](https://azure.microsoft.com/services/data-factory/). 

V této fázi vyvíjíte architekturu řešení datového kanálu. Vývoj kanálu souběžně s další fází projektu datové vědy. V závislosti na vašich obchodních potřebách a omezeních stávajících systémů, do kterých je toto řešení integrováno, může být kanál jednou z následujících možností: 

   * Na základě dávky
   * Streamování nebo v reálném čase 
   * Hybridní 

## <a name="artifacts"></a>Artefakty
V této fázi jsou uvedeny výstupy:

   * [Sestava kvality dat](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): Tato sestava obsahuje souhrny dat, vztahy mezi jednotlivými atributy a cílem, pořadí proměnných a další. Nástroj [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) poskytovaný jako součást protokolu TDSP může tuto sestavu rychle generovat v libovolné tabulkové datové sadě, například v souboru CSV nebo v relační tabulce. 
   * **Architektura řešení**: Architektura řešení může být diagram nebo popis datového kanálu, který slouží ke spuštění vyhodnocování nebo předpovědi na nová data po vytvoření modelu. Obsahuje také kanál pro přeškolit model na základě nových dat. Při použití šablony struktury adresáře TDSP uložte dokument do adresáře [aplikace Project.](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project)
   * **Rozhodnutí kontrolního bodu**: Než začnete plně funkční inženýrství a vytváření modelů, můžete přehodnotit projekt k určení, zda očekávaná hodnota je dostatečná k pokračování jeho sledování. Můžete být například připraveni pokračovat, potřebujete shromáždit další data nebo projekt opustit, protože data neexistují, aby odpověděli na otázku.

## <a name="next-steps"></a>Další kroky

Zde jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Principy podniku](lifecycle-business-understanding.md)
   2. [Získávání a pochopení dat](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníky](lifecycle-acceptance.md)

Poskytujeme úplné návody, které ukazují všechny kroky v procesu pro konkrétní scénáře. Ukázkový [článek obsahuje](walkthroughs.md) seznam scénářů s odkazy a popisy miniatur. Návody ilustrují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu k vytvoření inteligentní aplikace. 

Příklady kroků v TDSP, které používají Azure Machine Learning Studio, najdete [v tématu Použití TDSP s Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data).
