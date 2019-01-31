---
title: Identifikace scénářů a plánování procesu analýzy - vědecké zpracování týmových dat
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
ms.openlocfilehash: 0cf9ffddffe7f97008e69c9c4fb7945faeb779da
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469596"
---
# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Jak určit scénáře a plánovat zpracování dat pokročilé analýzy
Jaké prostředky můžete plán zahrnout při nastavování prostředí pro pokročilé analýzy zpracování pro datovou sadu? Tento článek navrhuje řadu otázky, které pomáhají identifikovat úloh a prostředků odpovídající vašemu scénáři. Pořadí hrubý nástin postupu pro prediktivní analýzy je popsaný v [novinky zpracování týmových dat vědy (TDSP)?](overview.md). Každý z těchto kroků vyžaduje konkrétní prostředky pro úlohy, které jsou relevantní pro váš konkrétní scénář. Klíčových otázek k identifikaci vašeho scénáře se týkají logistiky dat, charakteristiky, kvalitu datové sady, nástroje a jazyky, které chcete provést analýzu.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Logistické dotazy: umístění dat a pohybu
Logistické dotazy se týkají umístění **zdroj dat**, **cílového místa** v Azure a požadavky pro přesun dat, včetně plánu, velikost a zahrnutých prostředků. Data možná muset přesunout několikrát během analýzy. Běžný scénář, kdy je pro přesun místních dat do nějakou formu úložiště v Azure a pak do nástroje Machine Learning Studio.

1. **Jaký je váš zdroj dat?** Je místní nebo v cloudu? Příklad:
   
   * Data je veřejně dostupná na adresu HTTP.
   * Data se nacházejí v místní nebo síťové umístění souboru.
   * Data jsou v databázi serveru SQL Server.
   * Data uložená v kontejneru služby Azure storage
2. **Co je Azure cílové?** Pokud musí být pro zpracování nebo modelování? Příklad:
   
   * Azure Blob Storage
   * Databáze SQL Azure
   * SQL Server na virtuálním počítači Azure
   * HDInsight (Hadoop v Azure) nebo tabulek Hive
   * Azure Machine Learning
   * Možnost připojit Azure virtuálních pevných disků.
3. **Jak se to pro přesun dat?** Postupy a prostředky dostupné pro ingestování nebo načtení dat do různých jiného úložiště a zpracování prostředí jsou popsány v následujících článcích:
   
   * [Načtení dat do prostředí úložiště pro analýzu](ingest-data.md)
   * [Import cvičných dat do Azure Machine Learning Studio z různých zdrojů dat](../studio/import-data.md).
4. **Data musí být přesunuty v pravidelných intervalech nebo změněny během migrace?** Zvažte použití Azure Data Factory (ADF), pokud je potřeba data neustále migrovat, zejména v případě, že hybridní scénář, který přistupuje k místním a cloudovým prostředkům se jedná o nebo při dat je zpracováván jako transakce nebo je potřeba upravit nebo mít obchodní logiky, které jsou přidány do to při migraci. Další informace najdete v tématu [přesun dat z místních SQL serveru do SQL Azure s Azure Data Factory](move-sql-azure-adf.md)
5. **Kolik dat je přesunout do Azure?** Datové sady, které jsou značně velká může překročit kapacitu úložiště určité prostředí. Příklad najdete v diskuzi o omezení velikosti pro Machine Learning Studio v další části. V takových případech lze vzorek dat během analýzy. Podrobnosti o tom, jak dolů – ukázkové datové sady v různých prostředích Azure najdete v tématu [ukázkových dat v vědecké zpracování týmových dat](sample-data.md).

## <a name="data-characteristics-questions-type-format-and-size"></a>Otázky ohledně dat charakteristiky: typ, formátu a velikost
Tyto dotazy jsou klíčem k plánování vašeho úložiště a zpracování prostředí, každý z nich jsou vhodné pro různé typy dat a každý z nich mají určitá omezení.

1. **Jaké jsou typy dat?** Příklad:
   
   * Číselné
   * Kategorické
   * Řetězce
   * Binární hodnota
2. **Jak se vaše data ve formátu?** Příklad:
   
   * Oddělený čárkami (CSV) nebo oddělené tabulátorem (TSV) plochých souborů
   * Komprimované nebo nekomprimovaný
   * Objekty BLOB Azure
   * Tabulek Hadoop Hive
   * Tabulek systému SQL Server
3. **Jak velké jsou vaše data?**
   
   * Malé: Menší než 2 GB
   * Střední: Větší než 2 GB a menší než 10 GB
   * Velké: Větší než 10 GB

Vezměme si jako příklad prostředí Azure Machine Learning Studio:

* Seznam datových formátů a typy podporované v aplikaci Azure Machine Learning Studio najdete v tématu [formáty dat a datové typy podporované](../studio/import-data.md#data-formats-and-data-types-supported) oddílu.


Informace o omezení použít v procesu analýzy dalšími službami Azure, najdete v části [předplatné Azure a limity, kvóty a omezení](../../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Otázky ohledně dat kvality: zkoumání a předběžného zpracování
1. **Co můžete vědět o data?** Zkoumání dat získat znalosti o jeho základními charakteristikami. Jaké vzorce a trendy jeho vykazuje, jaké odlehlé hodnoty obsahuje nebo jak mnoho hodnot chybí. Tento krok je důležitý pro stanovení rozsahu předběžného zpracování potřebné pro formulování hypotézy, které by mohly navrhne nejvhodnější funkce nebo zadejte analýzy a vypracování plánů pro shromažďování dalších dat. Výpočet popisný statistiky a vykreslení vizualizace jsou užitečné techniky pro kontrolu dat. Podrobnosti o tom, jak prozkoumat datovou sadu v různých prostředích Azure najdete v tématu [zkoumání dat v vědecké zpracování týmových dat](explore-data.md).
2. **Vyžaduje data předběžného zpracování nebo čištění?**
   Předběžné zpracování a čištění dat jsou důležité úkoly, které obvykle musí být provedeny před datové sady se dá efektivně využít pro machine learning. Nezpracovaná data se často aktivní nebo nespolehlivé a může být chybějící hodnoty. Pomocí těchto dat pro modelování můžete vytvářet zavádějící výsledky. Popis najdete v tématu [úlohy k přípravě dat pro rozšířené strojové učení](prepare-data.md).

## <a name="tools-and-languages-questions"></a>Otázky týkající se nástroje a jazyky
Existuje mnoho možností v závislosti na tom, jaké jazyky a vývojová prostředí nebo nástrojů potřebujete nebo jsou nejvíce conformable pomocí.

1. **Jaké jazyky budete chtít použít pro analýzu?**  
   
   * R
   * Python
   * SQL
2. **Jaké nástroje byste měli použít pro analýzu dat?**
   
   * [Prostředí Azure Powershell](/powershell/azure/overview) -skriptovací jazyk, který používá ke správě vašich prostředků Azure v skriptovací jazyk.
   * [Azure Machine Learning Studio](../studio/what-is-ml-studio.md)
   * [Společnosti Revolution Analytics](https://www.microsoft.com/sql-server/machinelearningserver)
   * [RStudio](http://www.rstudio.com)
   * [Python Tools for Visual Studio](https://aka.ms/ptvsdocs)
   * [Anaconda](https://www.continuum.io/why-anaconda)
   * [Poznámkové bloky Jupyter](http://jupyter.org/)
   * [Microsoft Power BI](https://powerbi.microsoft.com)

## <a name="identify-your-advanced-analytics-scenario"></a>Určení scénáře pokročilých analýz
Jakmile jste odpověděli na otázky v předchozí části, jste připraveni k určení, který scénář nejvhodnější vyhovuje vašemu případu. Vzorové scénáře jsou popsány v [scénáře pro pokročilé analýzy ve službě Azure Machine Learning](plan-sample-scenarios.md).

