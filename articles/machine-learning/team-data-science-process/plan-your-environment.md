---
title: Scénáře identifikovat a naplánovat váš proces analytics - Azure | Microsoft Docs
description: Plán pro pokročilou analýzu v úvahu řadu klíčové otázky.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 421520dd-7728-4d29-889c-ebe6a0a6fb07
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: d2b982669f126695e3e5baefce24853269947536
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Jak určit scénáře a plánovat zpracování dat pokročilé analýzy
Jaké prostředky měli naplánovat při nastavení prostředí pro provádět pokročilé analýzy na datovou sadu? Tento článek navrhuje řadu otázky, snadněji identifikovat úlohy a prostředky, které jsou relevantní váš scénář. Pořadí kroků pro prediktivní analýzy popsané v [co je proces pro vědecké účely Data Team (TDSP)?](overview.md). Každý z těchto kroků pro úlohy, které jsou relevantní pro konkrétní scénář vyžaduje konkrétní prostředky. Klíčové otázky k identifikaci váš scénář se týkají dat logistiky charakteristiky, kvalitu nástroje a jazyky, které chcete provést analýzu a datové sady.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Logistic dotazy: dat – umístění a přesun
Logistic dotazy se týkají umístění **zdroj dat**, **cílového místa** v Azure a požadavky pro přesun dat, včetně plánu, velikost a prostředků zahrnutých. Data možná potřebujete přesunout několikrát během analýzy. Obvyklým scénářem je přesunout místní data do určitou formu úložiště v Azure a pak do nástroje Machine Learning Studio.

1. **Co je zdroj dat?** Je místní nebo v cloudu? Příklad:
   
   * Data jsou veřejně dostupné v adresa protokolu HTTP.
   * Data se nachází v místní nebo síťové umístění souboru.
   * Data jsou v databázi systému SQL Server.
   * Data jsou uložena v kontejneru úložiště Azure
2. **Co je Azure cílové?** Kde nemusí být pro zpracování nebo modelování? Příklad:
   
   * Azure Blob Storage
   * Databáze SQL Azure
   * SQL Server na virtuálním počítači Azure
   * HDInsight (Hadoop v Azure) nebo tabulek Hive
   * Azure Machine Learning
   * Připojit Azure virtuální pevné disky.
3. **Jak budete přesouvat data?** Postupy a prostředky, které jsou k dispozici ingestování nebo načtení dat do různých jiného úložiště a zpracování prostředí jsou uvedeny v následujících článcích:
   
   * [Načtení dat do úložiště prostředí pro analýzu](ingest-data.md)
   * [Importu trénovacích dat do Azure Machine Learning Studio z různých zdrojů dat](../studio/import-data.md).
4. **Potřebuje přesunout v pravidelných intervalech nebo změnit během migrace dat?** Zvážit použití Azure Data Factory (ADF), když data je třeba migrovat průběžně, zvlášť pokud hybridní scénář, který přistupuje k místní a cloudové prostředky je zahrnuta nebo když dat je zpracován, nebo musí být upraven nebo mít obchodní logiky přidána průběhu migrované. Další informace najdete v tématu [přesun dat z místního serveru SQL do SQL Azure s Azure Data Factory](move-sql-azure-adf.md)
5. **Kolik dat je přesunuta do Azure?** Datové sady, které jsou velmi velký může překročit kapacitu úložiště určité prostředí. Příklad najdete v tématu diskuzi o omezení velikosti pro Machine Learning Studio v další části. V takových případech ukázkových dat lze během analýzy. Podrobnosti o tom, jak nižší sample datovou sadu v různých prostředích Azure najdete v tématu [vzorová data v procesu Team datové vědy](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Otázky ohledně dat charakteristiky: velikost, typ a formát
Tyto otázky jsou klíčem k plánování vašeho úložiště a zpracování prostředí, jež jsou vhodné pro různé typy dat a každý z nich mají určitá omezení.

1. **Jaké jsou typy dat?** Například:
   
   * Číselné
   * Kategorické
   * Řetězce
   * Binární hodnota
2. **Vaše data formátování?** Například:
   
   * Textový soubor s oddělovači (CSV) nebo plochých souborů oddělené tabulátory (TSV)
   * Komprimované nebo nekomprimovaným
   * Azure BLOB
   * Tabulek Hadoop Hive
   * Tabulky serveru SQL Server
3. **Jak velké jsou vaše data?**
   
   * Malá: menší než 2 GB
   * Střední: Větší než 2 GB a menší než 10 GB
   * Velké: Větší než 10 GB

Třeba proveďte v prostředí Azure Machine Learning Studio:

* Seznam formáty dat a typy podporované systémem Azure Machine Learning Studio najdete v tématu [formáty dat a datové typy podporované](../studio/import-data.md#data-formats-and-data-types-supported) části.
* Informace o omezení dat pro Azure Machine Learning Studio najdete v tématu **jak velká může být datová sada pro mé moduly?** části [import a export dat pro Machine Learning](../studio/faq.md#machine-learning-studio-questions)

Informace o omezeních jinými službami Azure používá v procesu analytics najdete v tématu [předplatné Azure a omezení služby, kvóty a omezení](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Otázky ohledně kvality dat: zkoumání a předběžného zpracování
1. **Co víte o svá data?** Prozkoumejte data k získání pochopení jeho základními charakteristikami. Jaké vzorů či schémat nebo trendů ho vykazuje, jaké odlehlých má nebo počet hodnot chybí. Tento krok je důležitý pro stanovení rozsahu předběžné zpracování pro formulování hypotézy, které by mohla naznačovat nejvhodnější funkce nebo zadejte analýzy a formulování plány pro shromažďování dalších dat potřeby. Výpočet popisný statistiky a vykreslení vizualizace jsou užitečné techniky pro data kontroly. Podrobnosti o tom, jak prozkoumat datovou sadu v různých prostředích Azure najdete v tématu [zkoumat data v procesu Team datové vědy](explore-data.md).
2. **Vyžaduje data předem zpracování nebo čištění?**
   Předběžné zpracování a vyčištění dat jsou důležité úkoly, které obvykle musí být provedeny před datové sady je možné efektivně pro machine learning. Nezpracovaná data, je často aktivní nebo nespolehlivé a může být chybějící hodnoty. Pomocí těchto údajů pro modelování může vytvářet zavádějící výsledky. Popis najdete v tématu [úkoly přípravy dat pro vylepšený strojového učení](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Otázky týkající se nástroje a jazyky
Existuje mnoho možností v závislosti na tom, jaké jazyky a vývojových prostředích nebo nástroje potřebujete nebo jste nejvíce conformable pomocí.

1. **Jaké jazyky chcete použít pro analýzu?**  
   
   * R
   * Python
   * SQL
2. **Jaké nástroje můžete použít pro analýzu dat?**
   
   * [Microsoft Azure Powershell](/powershell/azure/overview) -skriptovací jazyk používají ke správě prostředků Azure v skriptovací jazyk.
   * [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
   * [Revolution Analytics](http://www.revolutionanalytics.com/revolution-r-open)
   * [Rstudia](http://www.rstudio.com)
   * [Python Tools for Visual Studio](http://microsoft.github.io/PTVS/)
   * [Anaconda](https://www.continuum.io/why-anaconda)
   * [Poznámkové bloky Jupyter](http://jupyter.org/)
   * [Microsoft Power BI](http://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Identifikovat váš scénář pokročilou analýzu
Jakmile jste odpověděli na otázky v předchozí části, jste připraveni k určení scénář, který nejlepší vyhovuje váš případ. Vzorové scénáře jsou uvedeny v [scénáře pro pokročilou analýzu v Azure Machine Learning](plan-sample-scenarios.md).

