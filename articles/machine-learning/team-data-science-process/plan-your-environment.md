---
title: Identifikace scénářů a plánování procesu analýzy - vědecké zpracování týmových dat | Azure Machine Learning
description: Identifikujte scénáře a plánovat zpracování dat pokročilé analýzy s ohledem na několik klíčových otázek.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a8ca08d93992a6e1bfe8ae24b83354503154ef31
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416152"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Jak určit scénáře a plánovat zpracování dat pokročilé analýzy

Jaké zdroje jsou potřebné pro vytvoření prostředí, které můžete provádět pokročilé analýzy zpracování pro datovou sadu? Tento článek navrhuje řadu otázky, které mohou pomoci identifikovat úloh a prostředků odpovídající vašemu scénáři.

Další informace o pořadí kroků pro prediktivní analýzy, najdete v článku [co je zpracování vědy dat (TDSP) týmu](overview.md). Každý krok vyžaduje konkrétní prostředky pro úlohy, které jsou relevantní pro váš konkrétní scénář.

Odpověď klíčových otázek v těchto oblastech k identifikaci vašemu scénáři:

* logistiky dat
* Vlastnosti dat
* Datová sada kvality
* oblíbených nástrojů a jazyků

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Logistické dotazy: umístění dat a pohybu

Na logistické otázky, které zahrnují následující položky:

* umístění zdroje dat
* cílového místa v Azure
* požadavky pro přesun dat, včetně plánu, velikost a zahrnutých prostředků

Budete muset přesunout data několikrát během analýzy. Běžný scénář, kdy je pro přesun místních dat do nějakou formu úložiště v Azure a pak do nástroje Machine Learning Studio.

### <a name="what-is-your-data-source"></a>Jaký je váš zdroj dat?

Jsou vaše data, místní nebo v cloudu? Je to možné umístění patří:

* veřejně dostupnou adresu HTTP
* místní nebo síťové umístění souboru
* databáze SQL serveru
* Kontejner úložiště Azure

### <a name="what-is-the-azure-destination"></a>Co je Azure cílové?

Kde vaše data musí být pro zpracování nebo modelování? 

* Azure Blob Storage
* Databáze SQL Azure
* SQL Server na virtuálním počítači Azure
* HDInsight (Hadoop v Azure) nebo tabulek Hive
* Azure Machine Learning
* Možnost připojit Azure virtuálních pevných disků

### <a name="how-are-you-going-to-move-the-data"></a>Jak se to pro přesun dat?

Postupy a zdroje pro ingestování nebo načtení dat do různých jiného úložiště a zpracování prostředí naleznete v tématu:

* [Načtení dat do prostředí úložiště pro analýzu](ingest-data.md)
* [Import cvičných dat do Azure Machine Learning Studio z různých zdrojů dat](../studio/import-data.md)

### <a name="does-the-data-need-to-be-moved-on-a-regular-schedule-or-modified-during-migration"></a>Data musí být přesunuty v pravidelných intervalech nebo změněny během migrace?

Zvažte použití Azure Data Factory (ADF), když je potřeba průběžně migrovat data. ADF může být užitečné pro:

* hybridní scénář, který zahrnuje i v místním a cloudovým prostředkům
* scénář, ve kterém je transakční data, upravit nebo změnit obchodní logiku v průběhu migrované

Další informace najdete v tématu [přesun dat z místních SQL serveru do SQL Azure s Azure Data Factory](move-sql-azure-adf.md).

### <a name="how-much-of-the-data-is-to-be-moved-to-azure"></a>Kolik dat je přesunout do Azure?

Velmi velkých datových sad může překročit kapacitu úložiště určité prostředí. Příklad najdete v diskuzi o omezení velikosti pro Machine Learning Studio v další části. V takovém případě můžete použít vzorek dat během analýzy. Podrobnosti o tom, jak dolů – ukázkové datové sady v různých prostředích Azure najdete v tématu [ukázkových dat v vědecké zpracování týmových dat](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Otázky ohledně dat charakteristiky: typ, formátu a velikost

Tyto dotazy jsou klíčem k plánování vašeho úložiště a zpracování prostředí. Bude pomáhají zvolte příslušné scénář pro datový typ a pochopení omezení.

### <a name="what-are-the-data-types"></a>Jaké jsou typy dat?

* Číselné
* Kategorické
* Řetězce
* Binární hodnota

### <a name="how-is-your-data-formatted"></a>Jak se vaše data ve formátu?

* Oddělený čárkami (CSV) nebo oddělené tabulátorem (TSV) plochých souborů
* Komprimované nebo nekomprimovaný
* Objekty BLOB Azure
* Tabulek Hadoop Hive
* Tabulek systému SQL Server

### <a name="how-large-is-your-data"></a>Jak velké jsou vaše data?

* Malé: Menší než 2 GB
* Střední: Větší než 2 GB a menší než 10 GB
* Velké: Větší než 10 GB

Vezměme si jako příklad prostředí Azure Machine Learning Studio:

* Seznam datových formátů a typy podporované v aplikaci Azure Machine Learning Studio najdete v tématu [formáty dat a datové typy podporované](../studio/import-data.md#supported-data-formats-and-data-types) oddílu.
* Informace o omezení použít v procesu analýzy dalšími službami Azure, najdete v části [předplatné Azure a limity, kvóty a omezení](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Otázky ohledně dat kvality: zkoumání a předběžného zpracování

### <a name="what-do-you-know-about-your-data"></a>Co můžete vědět o data?

Pochopit základní vlastnosti týkající se vašich dat:

* Co vzory nebo ji trendů dodatků
* Jaké odlehlé hodnoty, které obsahuje
* Kolik hodnot nebyly nalezeny

Tento krok je důležitý pro vám pomůže:

* Určit, kolik předběžného zpracování je potřeba.
* Formulovat hypotézy, které naznačují nejvhodnější funkce nebo typ analýzy
* Formulování plány pro shromažďování dalších dat

Techniky pro kontrolu dat. zahrnují výpočet popisný statistiky a vizualizace, který. Podrobnosti o tom, jak prozkoumat datovou sadu v různých prostředích Azure najdete v tématu [zkoumání dat v vědecké zpracování týmových dat](explore-data.md).

### <a name="does-the-data-require-preprocessing-or-cleaning"></a>Vyžaduje data předběžného zpracování nebo čištění?

Může být potřeba předběžně zpracovat a vyčistit data před použitím datové sady efektivně pro machine learning. Nezpracovaná data se často aktivní nebo nespolehlivé. Může být chybějící hodnoty. Pomocí těchto dat pro modelování můžete vytvářet zavádějící výsledky. Popis najdete v tématu [úlohy k přípravě dat pro rozšířené strojové učení](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Otázky týkající se nástroje a jazyky

Existuje mnoho možností pro jazyky, vývojové prostředí a nástroje. Mějte na paměti z vašich potřeb a preferencí.

### <a name="what-languages-do-you-prefer-to-use-for-analysis"></a>Jaké jazyky budete chtít použít pro analýzu?

* R
* Python
* SQL

### <a name="what-tools-should-you-use-for-data-analysis"></a>Jaké nástroje byste měli použít pro analýzu dat?

* [Prostředí Azure Powershell](/powershell/azure/overview) -skriptovací jazyk, který používá ke správě vašich prostředků Azure v skriptovací jazyk
* [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
* [Společnosti Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
* [RStudio](http://www.rstudio.com)
* [Python Tools for Visual Studio](https://aka.ms/ptvsdocs)
* [Anaconda](https://www.continuum.io/why-anaconda)
* [Poznámkové bloky Jupyter](http://jupyter.org/)
* [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Určení scénáře pokročilých analýz

Poté, co jste odpověděli na otázky v předchozí části, jste připraveni k určení, který scénář nejvhodnější vyhovuje vašemu případu. Vzorové scénáře jsou popsány v [scénáře pro pokročilé analýzy ve službě Azure Machine Learning](plan-sample-scenarios.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Co je zpracování týmových dat vědy (TDSP)?](overview.md)