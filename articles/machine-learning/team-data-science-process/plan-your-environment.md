---
title: Identifikujte scénáře a naplánujte proces analýzy – vědecký proces týmových dat | Azure Machine Learning
description: Identifikujte scénáře a naplánujte zpracování dat pro pokročilé analýzy tím, že zvažujete řadu klíčových otázek.
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
ms.openlocfilehash: 603628e24d7ccdffeafea392ab56aa7e47781a37
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91296480"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Jak určit scénáře a plánovat zpracování dat pokročilé analýzy

Jaké prostředky potřebujete k vytvoření prostředí, které umožňuje provádět pokročilé analytické zpracování na datové sadě? Tento článek navrhuje několik otázek, které vám pomůžou s identifikací úloh a prostředků, které jsou relevantní pro váš scénář.

Další informace o pořadí nejdůležitějších kroků pro prediktivní analýzy najdete v tématu [co je vědecký proces týmového zpracování dat (TDSP)](overview.md). Každý krok vyžaduje konkrétní prostředky pro úlohy, které jsou důležité pro konkrétní scénář.

Odpovězte na klíčové otázky v následujících oblastech a identifikujte svůj scénář:

* Logistika dat
* charakteristiky dat
* kvalita datové sady
* preferované nástroje a jazyky

## <a name="logistic-questions-data-locations-and-movement"></a>Logistické otázky: umístění a přesun dat

Logistické otázky zahrnují následující položky:

* umístění zdroje dat
* cíl cíle v Azure
* požadavky na přesun dat, včetně plánu, množství a souvisejících prostředků

Možná budete muset data během procesu analýzy několikrát přesunout. Běžným scénářem je přesunout místní data do nějaké formy úložiště v Azure a potom do Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Jaký je váš zdroj dat?

Jsou vaše data v místním nebo cloudovém prostředí? Mezi možná umístění patří:

* veřejně dostupná adresa HTTP
* umístění místního nebo síťového souboru
* Databáze SQL Serveru
* kontejner Azure Storage

### <a name="what-is-the-azure-destination"></a>Co je to cíl Azure?

Kde jsou vaše data potřeba pro zpracování nebo modelování? 

* Azure Blob Storage
* Databáze SQL Azure
* SQL Server na virtuálním počítači Azure
* HDInsight (Hadoop v Azure) nebo tabulky podregistru
* Azure Machine Learning
* Připojitelné virtuální pevné disky Azure

### <a name="how-are-you-going-to-move-the-data"></a>Jak se budou data přesouvat?

Postupy a prostředky k ingestování nebo načítání dat do nejrůznějších různých prostředí úložišť a zpracování najdete v těchto tématech:

* [Načtení dat do prostředí úložiště pro analýzu](ingest-data.md)
* [Import školicích dat do Azure Machine Learning Studio (Classic) z různých zdrojů dat](../classic/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Je potřeba data při migraci přesunout podle pravidelného plánu nebo změnit?

Zvažte použití Azure Data Factory (ADF), když je potřeba průběžně migrovat data. ADF může být užitečné pro:

* hybridní scénář, který zahrnuje místní i cloudové prostředky
* scénář, ve kterém jsou data v průběhu migrace zpracovávána, upravena nebo měněna obchodní logikou

Další informace najdete v tématu [přesun dat z databáze SQL Server do SQL Azure pomocí Azure Data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Kolik dat se má přesunout do Azure?

Velké datové sady mohou překročit kapacitu úložiště určitých prostředí. Příklad najdete v diskuzi o omezeních velikosti pro Machine Learning Studio (Classic) v následující části. V takových případech můžete použít ukázku dat během analýzy. Podrobnosti o tom, jak vytvořit ukázkovou datovou sadu v různých prostředích Azure, najdete v tématu [vzorová data v rámci vědeckého zpracování týmových dat](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Otázky vlastností dat: typ, formát a velikost

Tyto otázky jsou klíčem k plánování prostředí úložiště a zpracování. Pomůžou vám zvolit vhodný scénář pro datový typ a pochopit jakákoli omezení.

### <a name="what-are-the-data-types"></a>Jaké jsou datové typy?

* Číselné
* Kategorické
* Řetězce
* Binární

### <a name="how-is-your-data-formatted"></a>Jak vaše data jsou formátovaná?

* Ploché oddělené čárkami (CSV) nebo oddělených tabulátory (TSV)
* Komprimovaná nebo nekomprimovaná
* Objekty blob Azure
* Tabulky podregistru Hadoop
* SQL Server tabulky

### <a name="how-large-is-your-data"></a>Jak velká jsou vaše data?

* Malý: méně než 2 GB
* Střední: větší než 2 GB a menší než 10 GB
* Velký: větší než 10 GB

Vezměte v úvahu například prostředí Azure Machine Learning Studio (Classic):

* Seznam formátů dat a typů podporovaných nástrojem Azure Machine Learning Studio najdete v části [formáty dat a podporované datové typy](../classic/import-data.md#supported-data-formats-and-data-types) .
* Informace o omezeních ostatních služeb Azure, které se používají v procesu analýzy, najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Otázky kvality dat: průzkum a předběžné zpracování

### <a name="what-do-you-know-about-your-data"></a>Co víte o vašich datech?

Seznamte se se základními charakteristikami vašich dat:

* Jaké modely nebo trendy se vykazují
* Co všechno má
* Kolik hodnot chybí

Tento krok je důležitý, abychom vám pomohli:

* Určete, kolik předběžných zpracování je potřeba.
* Formulujte hypotézu, která navrhuje nejvhodnější funkce nebo typ analýzy.
* Formulujte plány pro další shromažďování dat

Mezi užitečné techniky pro kontrolu dat patří výpočty popisných statistik a vizualizace. Podrobnosti o tom, jak prozkoumat datovou sadu v různých prostředích Azure, najdete v tématu [prozkoumávání dat v rámci vědeckého zpracování týmových dat](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Vyžadují data předzpracování nebo čištění?

Aby bylo možné datovou sadu efektivně použít pro strojové učení, možná budete muset data předzpracovat a vyčistit. Nezpracovaná data jsou často vysokou úrovní šumu a nespolehlivá. Můžou chybět hodnoty. Použití takových dat pro modelování může způsobit zavádějící výsledky. Popis najdete v tématu [úlohy pro přípravu dat pro rozšířené strojové učení](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Dotazy k nástrojům a jazykům

K dispozici je celá řada možností pro jazyky, vývojová prostředí a nástroje. Buďte si vědomi vašich potřeb a preferencí.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Jaké jazyky preferujete pro účely analýzy?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Jaké nástroje byste měli použít k analýze dat?

* [Microsoft Azure PowerShell](/powershell/azure/) – skriptovací jazyk, který se používá ke správě prostředků Azure ve skriptovacím jazyce
* [Azure Machine Learning Studio](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
* [Analýza revoluce](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](https://www.rstudio.com)
* [Python Tools for Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.anaconda.com/)
* [Jupyter poznámkové bloky](https://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Určení scénáře pokročilé analýzy

Po zodpovězení otázek v předchozí části jste připraveni určit, který scénář nejlépe vyhovuje vašemu případu. Ukázkové scénáře jsou uvedené ve [scénářích pro pokročilou analýzu v Azure Machine Learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Co je vědecké zpracování týmových dat (TDSP)?](overview.md)
