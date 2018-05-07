---
title: Získávání dat a principy fáze životního cyklu proces vědecké účely dat Team - Azure | Microsoft Docs
description: Cíle, úlohy a úkoly pro získávání dat a principy fáze projekty vědecké zpracování dat
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 9ed793ac6a2efbd682bb739626c4d3a5f3551a3f
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="data-acquisition-and-understanding"></a>Získávání a pochopení dat

Tento článek popisuje cíle, úlohy a úkoly, které jsou přidružené k získávání dat a principy fáze z tým datové vědy procesu (TDSP). Tento proces zajišťuje doporučené životního cyklu, který můžete použít pro struktury vědecké zpracování dat projekty. Životní cyklus popisuje hlavní fází, které projekty obvykle provést, často interaktivně:

   1. **Pochopení obchodních**
   2. **Získávání dat a principy**
   3. **Modelování**
   4. **Nasazení**
   5. **Přijetí zákazníka**

Zde je vizuální reprezentace životního cyklu TDSP: 

![Životní cyklus TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cíle
* Vytvořit čistá, vysoce kvalitní datové sady, jehož relaci cílových proměnných odhalíte. Vyhledejte sadu dat v prostředí příslušné analýzy, jste připraveni k modelu.
* Vývoj architektury řešení kanálu data, která aktualizuje a pravidelně skóre data.

## <a name="how-to-do-it"></a>Jak to udělat
Existují tři hlavní úlohy v této fázi řešit:

   * **Zpracování příjmu dat** do cílové analytické prostředí.
   * **Data prozkoumat** k určení, pokud kvalitu dat je dostačující odpověď na otázku. 
   * **Nastavit datovém kanálu** skóre pro nová nebo pravidelně aktualizovat data.

### <a name="ingest-the-data"></a>Zpracování příjmu dat
Nastavte proces přesouvat data ze zdrojového umístění do cílového umístění, kde spouštíte analytics operace, jako je školení a předpovědi. Technické podrobnosti a možností, jak přesouvat data s různým službám Azure dat najdete v tématu [načtení dat do úložiště prostředí pro analýzu](ingest-data.md). 

### <a name="explore-the-data"></a>Zkoumání dat
Předtím, než je cvičení modely, budete muset vyvíjet zvukové povědomí o data. Skutečné datové sady jsou často aktivní, chybí hodnoty nebo hostitel jiných nesrovnalostí. Zadejte informace potřebné ke zpracování dat, než je připraven pro modelování a audit kvality dat. můžete shrnutí dat a vizualizaci. Tento proces je často iterativní.

TDSP poskytuje automatizované nástroj, nazývá [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), můžete vizualizovat data a připravit data souhrnné sestavy. Doporučujeme začínat IDEAR nejprve do zkoumat data, která vám pomůžou s vývojem počáteční data porozumění interaktivně s žádné kódování. Potom můžete psát vlastní kód pro zkoumání dat a vizualizaci. Pokyny k čištění data, najdete v části [úkoly přípravy dat pro vylepšený strojového učení](prepare-data.md).  

Jakmile budete spokojeni s kvality vyčištěny data, dalším krokem je pro lepší pochopení vzorů, které jsou obsaženy v datech. To vám pomůže vybrat a vyvinout odpovídající prediktivní model pro váš cíl. Vzhled pro důkaz, jak dobře propojená data je k cíli. Pak určete, jestli je dostatek dat přejdete na následující dalších kroků modelování. Tento proces je znovu, často iterativní. Možná budete muset najít nové zdroje dat s více přesný nebo relevantní více dat k posílení v datové sadě původně stanovených ve fázi předchozí. 

### <a name="set-up-a-data-pipeline"></a>Nastavit datovém kanálu
Kromě počáteční přijímání a čištění dat obvykle musíte nastavit proces skóre pro nová data nebo pravidelně aktualizuje data v rámci procesu probíhající učení. To provedete nastavením datovém kanálu nebo pracovního postupu. [Přesun dat z místní instance systému SQL Server k databázi SQL Azure s Azure Data Factory](move-sql-azure-adf.md) článek poskytuje příklad toho, jak nastavit kanál pomocí [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

V této fázi vyvíjet architektury řešení datového kanálu. Můžete vyvíjet kanálu paralelně s další fáze projektu vědecké účely data. V závislosti na obchodních potřeb a omezení do stávajících systémů, do kterých je integrovaná toto řešení může být kanálu jednu z těchto možností: 

   * Na základě batch
   * Streamování nebo v reálném čase. 
   * Hybrid 

## <a name="artifacts"></a>Artefakty
Tady jsou dodávky v této fázi:

   * [Sestava kvality dat](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md): Tato sestava obsahuje souhrny dat, vztahy mezi každý atribut a cíl, proměnné hodnocení a další. [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) nástroj, který poskytuje jako součást TDSP můžete rychle vygenerovat tuto sestavu na žádné tabulkové datové sady, jako je například soubor CSV nebo relační tabulky. 
   * **Architektura řešení**: Architektura řešení může být diagram nebo popis data kanálu, které potřebují k provedení vyhodnocování nebo předpovědi na nová data po vytvoření modelu. Obsahuje taky kanálu, který má být přeučování modelu na základě na nová data. Uložení dokumentu v [projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) directory při použití šablony TDSP directory struktura.
   * **Kontrolní bod rozhodnutí**: než začnete inženýrství plně funkční a vytváření modelů, prezentovaný projekt, který má-li určit, zda je očekávána hodnota dostatečná pokračujte usilovat o normalizaci ho. Může být například připraveni pokračovat, třeba shromažďovat další data, nebo zrušte projektu jako data neexistuje odpověď na otázku.

## <a name="next-steps"></a>Další postup

Tady jsou odkazy na každý krok v životním cyklu TDSP:

   1. [Pochopení obchodních](lifecycle-business-understanding.md)
   2. [Získávání dat a principy](lifecycle-data.md)
   3. [Modelování](lifecycle-modeling.md)
   4. [Nasazení](lifecycle-deployment.md)
   5. [Přijetí zákazníka](lifecycle-acceptance.md)

Poskytujeme návody úplného začátku do konce, které ukazují všechny kroky v procesu pro konkrétní scénáře. [Příklad návody](walkthroughs.md) článek obsahuje seznam scénářů s odkazy a popisy miniatur. Názorné postupy ukazují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu vytvoření inteligentního aplikace. 

Příklady, jak provést kroky v TDSPs, které používají Azure Machine Learning Studio, najdete v části [pomocí Azure Machine Learning TDSP](http://aka.ms/datascienceprocess).
