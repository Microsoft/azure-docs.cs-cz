---
title: Porovnání různých produktů pro strojové učení od Microsoftu – Azure | Microsoft Docs
description: Porovnejte produkty v široké nabídce Microsoftu pro sestavování, nasazování a správu modelů strojového učení. Rozhodněte, které produkty by byly vhodné pro vaše řešení.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 09/24/2018
ms.openlocfilehash: 182504373795b3cb0f2794acbed5e253ac6bc95c
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419555"
---
# <a name="what-are-the-machine-learning-product-options-from-microsoft"></a>Jaké produkty pro strojové učení nabízí Microsoft?

Microsoft nabízí širokou škálu produktů pro sestavování, nasazování a správu modelů strojového učení. Porovnejte si je a zvolte ty, které potřebujete, aby vývoj vašich řešení pro strojové učení byl co nejefektivnější.

| Produkt pro strojové učení | Co to je | Jaké nabízí možnosti |
|-|-|-|
| V cloudu | | |
| [Služba Azure Machine Learning](#azure-machine-learning-services) | Spravovaná cloudová služba pro strojové učení  | Trénování, nasazování a správa modelů v Azure s použitím Pythonu a rozhraní CLI |
| [Azure Machine Learning Studio](#azure-machine-learning-studio) | Vizuální rozhraní pro strojové učení na bázi přetahování myší | Sestavování a nasazování modelů pomocí předkonfigurovaných algoritmů a experimentování s nimi |
| [Azure Databricks](#azure-databricks) | Analytická platforma založená na Sparku | Sestavování a nasazování modelů a pracovních postupů pro data |
| [Azure Cognitive Services](#azure-cognitive-services) | Služby Azure s předkonfigurovanými modely umělé inteligence a strojového učení | Jednoduché přidávání inteligentních funkcí do vašich aplikací |
| [Azure Data Science Virtual Machine](#azure-data-science-virtual-machine) | Virtuální počítač s předinstalovanými nástroji pro datové vědy | Vývoj řešení pro strojové učení v předkonfigurovaném prostředí |
| Lokálně | | |
| [SQL Server Machine Learning Services](#sql-server-machine-learning-services) | Analytický modul vložený do SQL | Sestavování a nasazování modelů na SQL Serveru |
| [Microsoft Machine Learning Server](#microsoft-machine-learning-server) | Samostatný firemní server pro prediktivní analýzy | Sestavování a nasazování modelů v jazycích R a Python |
| Vývojářské nástroje | | |
| [ML.NET](#mlnet) | Multiplatformní sada SDK typu open source pro strojové učení | Vývoj řešení strojového učení pro aplikace .NET |
| [Windows ML](#windows-ml) | Platforma strojového učení pro Windows 10 | Vyhodnocování trénovaných modelů na zařízení s Windows 10 |

## <a name="azure-machine-learning-service"></a>Služba Azure Machine Learning

[Služba Azure Machine Learning](overview-what-is-azure-ml.md) (Preview) je plně spravovaná cloudová služba sloužící k trénování, nasazování a správě modelů strojového učení ve velkém měřítku. Plně podporuje open source technologie, takže můžete používat desítky tisíc volně dostupných balíčků Pythonu, například TensorFlow, PyTorch a scikit-learn. K dispozici jsou také plnohodnotné nástroje, například [poznámkové bloky Azure](https://notebooks.azure.com/), [poznámkové bloky Jupyter](http://jupyter.org) nebo [Visual Studio Code Tools for AI](https://visualstudio.microsoft.com/downloads/ai-tools-vscode/), které usnadňují zkoumání a transformace dat a následné trénování a vývoj modelů. Azure Machine Learning obsahuje funkce, které automatizují generování a ladění modelů, a to snadno, efektivně a přesně.

Pomocí služby Azure Machine Learning můžete trénovat, nasazovat a spravovat modely strojového učení s použitím Pythonu a rozhraní CLI ve velkém měřítku.

>[!Note]
> Azure Machine Learning si můžete vyzkoušet zdarma. Nevyžaduje se platební karta ani předplatné Azure. Začněte hned teď. https://azure.microsoft.com/free/

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

[Azure Machine Learning Studio](../studio/what-is-ml-studio.md) poskytuje interaktivní vizuální pracovní prostor, který vám umožní snadno a rychle vytvářet, testovat a nasazovat modely s využitím předpřipravených algoritmů strojového učení. Machine Learning Studio publikuje modely jako webové služby, které je možné snadno využívat ve vlastních aplikacích nebo nástrojích BI, například v Excelu.
Není nutné žádné programování – model strojového učení zkonstruujete propojením datových sad a analytických modulů na interaktivním plátně, které potom nasadíte pomocí pár kliknutí.

Machine Learning Studio použijte, pokud chcete modely vyvíjet a nasazovat bez nutnosti programovat.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) je analytická platforma založená na Apache Spark a optimalizovaná pro platformu cloudových služeb Microsoft Azure. Platforma Databricks poskytuje díky integraci s Azure nastavení jedním kliknutím, zjednodušené pracovní postupy a interaktivní pracovní prostor, který umožňuje spolupráci mezi datovými vědci, odborníky přes data a obchodními analytiky.
Pomocí kódu Python, R, Scala a SQL ve webových poznámkových blocích můžete u dat zadávat dotazy, vizualizovat je a modelovat.

Databricks použijte, pokud chcete na sestavování řešení strojového učení spolupracovat na platformě Apache Spark.

## <a name="azure-cognitive-services"></a>Azure Cognitive Services

[Azure Cognitive Services](/azure/cognitive-services/welcome) je sada rozhraní API, která umožňuje sestavovat aplikace využívající přirozené způsoby komunikace. Díky těmto rozhraním API dokážou vaše aplikace vidět, slyšet, mluvit, chápat a interpretovat potřeby uživatelů prostřednictvím pouhých pár řádků kódu. Do svých aplikací můžete snadno přidávat inteligentní funkce, jako jsou tyto: 

- Rozpoznávání emocí a pocitů
- Rozpoznávání obrazu a řeči
- Rozpoznávání jazyka (LUIS)
- Znalosti a hledání

Pomocí Cognitive Services můžete vyvíjet aplikace napříč různými zařízeními a platformami. Rozhraní API se neustále zlepšují a je snadné je nastavit.

## <a name="azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine

[Azure Data Science Virtual Machine](../data-science-virtual-machine/overview.md) je přizpůsobené prostředí virtuálního počítače v cloudu Microsoft Azure vytvořené speciálně pro účely datových věd. Obsahuje řadu oblíbených předinstalovaných nástrojů datové vědy a dalších funkcí a je předem nakonfigurovaná, abyste mohli hned začít sestavovat inteligentní aplikace pro pokročilou analýzu.
Data Science Virtual Machine je k dispozici ve verzích pro Windows i Linux Ubuntu (služba Azure Machine Learning není podporována v Linux CentOS).
Informace o konkrétní verzi a přehled jejích součástí najdete v tématu [Úvod k prostředí Azure Data Science Virtual Machine](../data-science-virtual-machine/overview.md).
Prostředí Data Science Virtual Machine je podporováno jako cíl pro službu Azure Machine Learning.

Data Science VM použijte, když potřebujete spustit nebo hostovat úlohy v jednom uzlu. nebo když potřebujete vzdáleně vertikálně navýšit kapacitu zpracování v jednom počítači.

## <a name="sql-server-machine-learning-services"></a>SQL Server Machine Learning Services

[Služba Microsoft Machine Learning pro SQL Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) přidává statistické analýzy, vizualizace dat a prediktivní analýzy v jazyce R a Python pro relační data v databázích SQL Serveru. Knihovny v jazycích R a Python od Microsoftu poskytují na SQL Serveru pokročilé modelování a algoritmy pro strojové učení, které můžou běžet paralelně a ve velkém měřítku.

SQL Server Machine Learning Services použijte, pokud potřebujete integrovanou umělou inteligenci a prediktivní analýzy v relačních datech na SQL Serveru.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

[Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server) je podnikový server pro hostování a správu paralelních a distribuovaných úloh procesů jazyka R a Python. Microsoft Machine Learning Server běží na systémech Linux, Windows, Hadoop a Apache Spark a je také k dispozici přes [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Poskytuje prováděcí modul pro řešení sestavená pomocí balíčků [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) a [MicrosoftML](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) a rozšiřuje open source jazyky R a Python doplněním podpory vysoce výkonných analýz, statistických analýz, strojového učení a velmi rozsáhlých datových sad. Tuto funkci zajišťují vlastnicky chráněné balíčky, které se instalují se serverem. Pro vývoj můžete používat integrovaná vývojová prostředí (IDE) jako [Nástroje R pro Visual Studio](https://www.visualstudio.com/vs/rtvs/) a [Python Tools for Visual Studio](https://www.visualstudio.com/vs/python/).

Microsoft Machine Learning Server použijte, pokud chcete na serveru sestavovat a provozovat modely sestavené v jazycích R a Python nebo distribuovat trénování na bázi R a Python ve velkém měřítku v clusteru Hadoop nebo Spark.

## <a name="mlnet"></a>ML.NET

[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/) je bezplatná open source a multiplatformní architektura pro strojové učení, která umožňuje vytvářet vlastní řešení pro strojové učení a integrovat je do vašich aplikací .NET.

ML.NET použijte, pokud chcete řešení pro strojové učení integrovat do svých aplikací .NET.

## <a name="windows-ml"></a>Windows ML

[Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/) umožňuje používat natrénované modely strojového učení ve vašich aplikacích a vyhodnocovat je místně na zařízeních s Windows 10.

Windows ML zvolte, pokud chcete používat natrénované modely strojového učení v rámci aplikací pro Windows.

## <a name="next-steps"></a>Další kroky

- Další informace o všech produktech pro vývoj umělé inteligence (AI) dostupných od Microsoftu najdete v tématu [Platforma Microsoft AI](https://www.microsoft.com/ai).
- Kurzy k vývoji řešení AI najdete na webu [Microsoft AI School](https://aischool.microsoft.com/learning-paths)
