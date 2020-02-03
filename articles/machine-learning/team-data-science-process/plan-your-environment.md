---
title: Identifikujte scénáře a naplánujte proces analýzy – vědecký proces týmových dat | Azure Machine Learning
description: Identifikujte scénáře a plánovat zpracování dat pokročilé analýzy s ohledem na několik klíčových otázek.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710325"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Jak určit scénáře a plánovat zpracování dat pokročilé analýzy

Jaké prostředky potřebujete k vytvoření prostředí, které umožňuje provádět pokročilé analytické zpracování na datové sadě? Tento článek navrhuje několik otázek, které vám pomůžou s identifikací úloh a prostředků, které jsou relevantní pro váš scénář.

Další informace o pořadí nejdůležitějších kroků pro prediktivní analýzy najdete v tématu [co je vědecký proces týmového zpracování dat (TDSP)](overview.md). Každý krok vyžaduje konkrétní prostředky pro úlohy, které jsou důležité pro konkrétní scénář.

Odpovězte na klíčové otázky v následujících oblastech a identifikujte svůj scénář:

* Logistika dat
* charakteristiky dat
* kvalita datové sady
* preferované nástroje a jazyky

## <a name="logistic-questions-data-locations-and-movement"></a>Logistické dotazy: umístění dat a pohybu

Logistické otázky zahrnují následující položky:

* umístění zdroje dat
* cíl cíle v Azure
* požadavky na přesun dat, včetně plánu, množství a souvisejících prostředků

Možná budete muset data během procesu analýzy několikrát přesunout. Běžný scénář, kdy je pro přesun místních dat do nějakou formu úložiště v Azure a pak do nástroje Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Jaký je váš zdroj dat?

Jsou vaše data v místním nebo cloudovém prostředí? Mezi možná umístění patří:

* veřejně dostupná adresa HTTP
* umístění místního nebo síťového souboru
* databáze SQL Server
* kontejner Azure Storage

### <a name="what-is-the-azure-destination"></a>Co je to cíl Azure?

Kde jsou vaše data potřeba pro zpracování nebo modelování? 

* Azure Blob Storage
* Databáze SQL Azure
* SQL Server na virtuálním počítači Azure
* HDInsight (Hadoop v Azure) nebo tabulek Hive
* Azure Machine Learning
* Připojitelné virtuální pevné disky Azure

### <a name="how-are-you-going-to-move-the-data"></a>Jak se budou data přesouvat?

Postupy a prostředky k ingestování nebo načítání dat do nejrůznějších různých prostředí úložišť a zpracování najdete v těchto tématech:

* [Načtení dat do prostředí úložiště pro analýzu](ingest-data.md)
* [Import školicích dat do Azure Machine Learning Studio (Classic) z různých zdrojů dat](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Je potřeba data při migraci přesunout podle pravidelného plánu nebo změnit?

Zvažte použití Azure Data Factory (ADF), když je potřeba průběžně migrovat data. ADF může být užitečné pro:

* hybridní scénář, který zahrnuje místní i cloudové prostředky
* scénář, ve kterém jsou data v průběhu migrace zpracovávána, upravena nebo měněna obchodní logikou

Další informace najdete v tématu [přesun dat z místního SQL serveru pro SQL Azure s Azure Data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Kolik dat se má přesunout do Azure?

Velké datové sady mohou překročit kapacitu úložiště určitých prostředí. Příklad najdete v diskuzi o omezeních velikosti pro Machine Learning Studio (Classic) v následující části. V takových případech můžete použít ukázku dat během analýzy. Podrobnosti o tom, jak vytvořit ukázkovou datovou sadu v různých prostředích Azure, najdete v tématu [vzorová data v rámci vědeckého zpracování týmových dat](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Otázky ohledně dat charakteristiky: typ, formátu a velikost

Tyto otázky jsou klíčem k plánování prostředí úložiště a zpracování. Pomůžou vám zvolit vhodný scénář pro datový typ a pochopit jakákoli omezení.

### <a name="what-are-the-data-types"></a>Jaké jsou datové typy?

* Číselné
* Kategorické
* Řetězce
* Binární hodnota

### <a name="how-is-your-data-formatted"></a>Jak vaše data jsou formátovaná?

* Oddělený čárkami (CSV) nebo oddělené tabulátorem (TSV) plochých souborů
* Komprimované nebo nekomprimovaný
* Objekty BLOB Azure
* Tabulek Hadoop Hive
* Tabulek systému SQL Server

### <a name="how-large-is-your-data"></a>Jak velká jsou vaše data?

* Malé: menší než 2 GB
* Střední: Větší než 2 GB a menší než 10 GB
* Velké: Větší než 10 GB

Vezměte v úvahu například prostředí Azure Machine Learning Studio (Classic):

* Seznam formátů dat a typů podporovaných nástrojem Azure Machine Learning Studio najdete v části [formáty dat a podporované datové typy](../studio/import-data.md#supported-data-formats-and-data-types) .
* Informace o omezeních ostatních služeb Azure, které se používají v procesu analýzy, najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Otázky ohledně dat kvality: zkoumání a předběžného zpracování

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

Aby bylo možné datovou sadu efektivně použít pro strojové učení, možná budete muset data předzpracovat a vyčistit. Nezpracovaná data jsou často vysokou úrovní šumu a nespolehlivá. Můžou chybět hodnoty. Pomocí těchto dat pro modelování můžete vytvářet zavádějící výsledky. Popis najdete v tématu [úlohy pro přípravu dat pro rozšířené strojové učení](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Otázky týkající se nástroje a jazyky

K dispozici je celá řada možností pro jazyky, vývojová prostředí a nástroje. Buďte si vědomi vašich potřeb a preferencí.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Jaké jazyky preferujete pro účely analýzy?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Jaké nástroje byste měli použít k analýze dat?

* [Microsoft Azure PowerShell](/powershell/azure/overview) – skriptovací jazyk, který se používá ke správě prostředků Azure ve skriptovacím jazyce
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [Analýza revoluce](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](https://www.rstudio.com)
* [Python Tools pro Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Poznámkové bloky Jupyter](https://jupyter.org/)
* [Power BI Microsoftu](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Určení scénáře pokročilých analýz

Po zodpovězení otázek v předchozí části jste připraveni určit, který scénář nejlépe vyhovuje vašemu případu. Ukázkové scénáře jsou uvedené ve [scénářích pro pokročilou analýzu v Azure Machine Learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Co je vědecké zpracování týmových dat (TDSP)?](overview.md)
