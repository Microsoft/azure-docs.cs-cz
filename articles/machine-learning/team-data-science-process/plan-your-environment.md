---
title: Identifikujte scénáře a naplánujte proces analýzy – proces vědecké vědy o týmových datech | Azure Machine Learning
description: Identifikujte scénáře a plánujte pokročilé zpracování analytických dat s ohledem na řadu klíčových otázek.
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
ms.openlocfilehash: b0b811a2b7ed432b7fc5015886b28337ca33424e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710325"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Jak určit scénáře a plánovat zpracování dat pokročilé analýzy

Jaké prostředky jsou potřebné k vytvoření prostředí, které může provádět pokročilé zpracování analýzy v datové sadě? Tento článek navrhuje řadu otázek, které mohou pomoci identifikovat úkoly a zdroje relevantní váš scénář.

Další informace o pořadí kroků vysoké úrovně pro prediktivní analýzy, najdete [v tématu Co je proces vědecké vědy o týmových datech (TDSP).](overview.md) Každý krok vyžaduje specifické zdroje pro úkoly relevantní pro konkrétní scénář.

Odpovězte na klíčové otázky v následujících oblastech a identifikujte svůj scénář:

* logistika dat
* charakteristiky údajů
* kvalita datovésady
* preferované nástroje a jazyky

## <a name="logistic-questions-data-locations-and-movement"></a>Logistické otázky: umístění dat a pohyb

Logistické otázky se týkají následujících položek:

* umístění zdroje dat
* cílová destinace v Azure
* požadavky na přesun údajů, včetně harmonogramu, částky a zdrojů, které jsou

Během procesu analýzy může být nutné data několikrát přesunout. Běžným scénářem je přesunutí místních dat do nějaké formy úložiště v Azure a potom do Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Jaký je váš zdroj dat?

Jsou vaše data místní nebo v cloudu? Mezi možná místa patří:

* veřejně dostupná HTTP adresa
* umístění místního nebo síťového souboru
* Databáze SQL Serveru
* kontejner úložiště Azure

### <a name="what-is-the-azure-destination"></a>Co je cíl Azure?

Kde musí být vaše data pro zpracování nebo modelování? 

* Azure Blob Storage
* Databáze SQL Azure
* SQL Server na virtuálním počítači Azure
* Tabulky HDInsight (Hadoop v Azure) nebo Hive
* Azure Machine Learning
* Připojitelné virtuální pevné disky Azure

### <a name="how-are-you-going-to-move-the-data"></a>Jak chceš přesunout data?

Postupy a prostředky pro ingestování nebo načítání dat do různých prostředí úložiště a zpracování naleznete v těchto tématech:

* [Načtení dat do prostředí úložiště pro analýzu](ingest-data.md)
* [Import trénovacích dat do Azure Machine Learning Studio (klasické) z různých zdrojů dat](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Je třeba data během migrace přesouvat podle pravidelného plánu nebo upravovat?

Zvažte použití Azure Data Factory (ADF) když je potřeba data neustále migrovat. ADF může být užitečné pro:

* hybridní scénář, který zahrnuje místní i cloudové prostředky
* scénář, kdy jsou data v průběhu migrace zpracována, změněna nebo změněna obchodní logikou

Další informace najdete v tématu [Přesun dat z místního serveru SQL do SQL Azure pomocí Azure Data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Kolik dat se má přesunout do Azure?

Velké datové sady mohou překročit kapacitu úložiště v určitých prostředích. Příklad najdete v tématu diskuse o omezení velikosti pro Machine Learning Studio (klasické) v další části. V takových případech můžete použít vzorek dat během analýzy. Podrobnosti o tom, jak odebrat vzorek datové sady v různých prostředích Azure, najdete [v tématu Ukázková data v procesu vědecké ho sloužiny týmových dat](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Otázky týkající se charakteristik dat: typ, formát a velikost

Tyto otázky jsou klíčem k plánování prostředí úložiště a zpracování. Pomohou vám vybrat vhodný scénář pro váš datový typ a pochopit všechna omezení.

### <a name="what-are-the-data-types"></a>Jaké jsou datové typy?

* Číselné
* Kategorické
* Řetězce
* binární

### <a name="how-is-your-data-formatted"></a>Jak jsou data formátována?

* Ploché soubory oddělené čárkami (CSV) nebo tabulátory (TSV)
* Komprimované nebo nekomprimované
* Objekty BLOB Azure
* Stoly Hadoop Hive
* Tabulky serveru SQL Server

### <a name="how-large-is-your-data"></a>Jak velká jsou vaše data?

* Malé: Méně než 2 GB
* Střední: Větší než 2 GB a méně než 10 GB
* Velký: Větší než 10 GB

Vezměte si prostředí Azure Machine Learning Studio (klasické) například:

* Seznam datových formátů a typů podporovaných službou Azure Machine Learning Studio najdete v článku [Podporované datové formáty a datové typy.](../studio/import-data.md#supported-data-formats-and-data-types)
* Informace o omezeních jiných služeb Azure používaných v procesu analýzy najdete v tématu [Limity předplatného a služeb Azure, kvóty a omezení](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Otázky kvality dat: průzkum a předběžné zpracování

### <a name="what-do-you-know-about-your-data"></a>Co víte o svých datech?

Seznamte se se základními charakteristikami svých dat:

* Jaké vzory nebo trendy vykazuje
* Jaké odlehlé hodnoty má
* Počet hodnot chybí

Tento krok je důležitý, aby vám pomohl:

* Určení, kolik předběžného zpracování je potřeba
* Formulovat hypotézy, které naznačují nejvhodnější rysy nebo typ analýzy
* Formulovat plány pro další sběr dat

Mezi užitečné techniky kontroly dat patří výpočty popisných statistik a vizualizace. Podrobnosti o tom, jak prozkoumat datovou sadu v různých prostředích Azure, najdete [v tématu Prozkoumání dat v procesu vědecké ho sloužiny týmových dat](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Vyžadují data předběžné zpracování nebo čištění?

Možná budete muset předzpracování mašlí a vyčistit data před efektivní matnou datpro strojové učení. Nezpracovaná data jsou často hlučná a nespolehlivá. Může to být chybějící hodnoty. Použití těchto dat pro modelování může vést k zavádějícím výsledkům. Popis najdete v [tématu Úkoly pro přípravu dat pro rozšířené strojové učení](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Nástroje a jazyky otázky

Existuje mnoho možností pro jazyky, vývojová prostředí a nástroje. Buďte si vědomi svých potřeb a preferencí.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Jaké jazyky dáváte přednost pro analýzu?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Jaké nástroje byste měli použít pro analýzu dat?

* [Microsoft Azure Powershell](/powershell/azure/overview) – skriptovací jazyk používaný ke správě prostředků Azure ve skriptovacím jazyce
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [Revoluční analytika](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](https://www.rstudio.com)
* [Python Tools pro Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Poznámkové bloky Jupyter](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identifikujte pokročilý scénář analýzy

Poté, co jste odpověděli na otázky v předchozí části, jste připraveni určit, který scénář nejlépe vyhovuje vašemu případu. Ukázkové scénáře jsou popsané ve [scénářích pro pokročilou analýzu v Azure Machine Learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Co je vědecké zpracování týmových dat (TDSP)?](overview.md)
