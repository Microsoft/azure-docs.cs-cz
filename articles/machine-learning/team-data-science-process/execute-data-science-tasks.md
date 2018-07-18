---
title: Spuštění úloh datových věd - Azure Machine Learning | Dokumentace Microsoftu
description: Jak můžete spustit projekt vědeckého zpracování dat v organizovaným, odborník přes data správy verzí a spolupráce.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: deguhath
ms.openlocfilehash: 19267429510207129b0229dc55fbd46f12977d5d
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112640"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Spuštění úloh datových věd: zkoumání, modelování a nasazení

Typické úloh vědeckého zpracování zahrnout zkoumání dat, modelování a nasazení. Tento článek popisuje, jak používat **interaktivní zkoumání dat, analýzu a vytváření sestav (IDEAR)** a **automatizované modelování a vytváření sestav (AMAR)** nástroje dokončit několik běžných úloh datové vědy například interaktivní zkoumání dat, analýzy dat, vykazování a vytvoření modelu. Popisuje také možnosti pro model nasazení do produkčního prostředí používají různé platformy, sady nástrojů a dat, jako je následující:

- [Azure Machine Learning](../service/index.yml)
- [SQL Server pomocí služby ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services#in-database-analytics-with-sql-server)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> Průzkum 

Můžete provádět mezi odborníky přes data, prozkoumávání a vytváření sestav v mnoha různými způsoby: pomocí knihoven nebo balíčků, které jsou k dispozici pro jazyk Python (například matplotlib) nebo s jazykem R (ggplot nebo mřížkových příkladu). Odborníci přes data můžete přizpůsobit podle potřeb zkoumání dat pro konkrétní scénáře takového kódu. Požadavky pro práci se strukturovanými daty se liší, který pro Nestrukturovaná data, jako je například text nebo obrázky. 

Produkty, jako je Azure Machine Learning Workbench také poskytují [Pokročilá Příprava dat](../desktop-workbench/tutorial-bikeshare-dataprep.md) tahání dat a zkoumání, včetně vytváření funkce. Uživatel měli rozhodnout na nástroje, knihovny a balíčky, které osvědčené suite jejich potřeb. 

Dodávky na konci této fáze je sestava průzkumu data. Sestava by měla poskytnout poměrně rozsáhlý náhled na data, která má být použit pro modelování a určuje, zda je vhodné, pokračujte ke kroku modelování data posouzení. Vědecké zpracování týmových dat (TDSP) nástroje popsané v následujících částech poloautomatických průzkumných, modelování a vytváření sestav také poskytuje standardizované zkoumání a modelování sestavy. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interaktivní zkoumání dat, analýzu a vytváření sestav pomocí nástroje IDEAR

Tento R markdown systémem nebo nástroj založený na Poznámkový blok Python poskytuje flexibilní a interaktivní nástroj pro vyhodnocení a prozkoumejte datových sad. Uživatelům můžete rychle vytvořit sestavy z datové sady s minimálním kódováním. Uživatelé mohou klepnutím na tlačítko Exportovat výsledky průzkumu nástroji pro interaktivní Konečná sestava, která může doručit do klientů nebo používá k rozhodování, na které proměnné k zahrnutí v kroku následné modelování.

V tuto chvíli nástroj funguje pouze na datové rámce v paměti. Soubor YAML, je potřeba zadat parametry datové sady má být zkoumána. Další informace najdete v tématu [IDEAR v TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a> Modelování

Existuje mnoho sad nástrojů a balíčky pro trénování modelů v různých jazycích. Odborníci přes data by měla můžete použít, který následně ty jsou zvyklí, tak dlouho, dokud jsou splněny důležité informace o výkonu týkající se přesnosti a latencí pro důležité obchodní použít případy a produkční scénáře.

V další části ukazuje, jak používat nástroj založené na jazyce R TDSP pro poloautomatických modelování. Tento nástroj AMAR lze použít ke generování řádku základní modely rychle stejně jako parametry, které musí být vyladěné tak, aby poskytují lepší provádění modelu.
Následující části model správy ukazuje, jak systém pro registraci a správu více modelů.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Model školení: modelování a vytváření sestav pomocí nástroje AMAR

[Automatizované modelování a vytváření sestav (AMAR) nástroj](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) poskytuje přizpůsobitelné poloautomatických nástroj k provedení vytvoření modelu s hyperparametrické sweeping a porovnat přesnost těchto modelech. 

Nástroj pro vytváření modelu je soubor R Markdown, který můžete spustit pro vytvoření samostatné výstupu protokolu HTML s obsah pro snadnou navigaci si jeho různé části. Tři algoritmy jsou spouštěny, když běží soubor Markdownu (spojit): Vyřešeno regresi využívající glmnet balíček, náhodných doménové struktury pomocí balíčku randomForest a zvýšení úrovně stromové struktury pomocí balíčku xgboost). Každá z těchto algoritmů vytváří trénovaného modelu. Přesnost tyto modely se následně porovnává a vykreslení funkce relativní důležitost jsou hlášeny. V současné době existují dva nástroje: jeden je pro úlohu binární klasifikace a jeden je pro úlohu regrese. Základní rozdíly mezi nimi je způsob, jak řídicí parametry a přesnost metriky jsou určené pro tyto úlohy učení. 

Soubor YAML slouží k určení:

- datový vstup (zdroje SQL nebo R-datového souboru) 
- jaká část dat se používá pro trénování a jaká část pro účely testování
- jaké algoritmy pro spuštění 
- Výběr ovládacího prvku parametry pro optimalizaci modelů:
    - křížové ověření 
    - Probíhá spuštění
    - přeložení křížového ověřování
- hyperparametrické sady jednotlivých algoritmů. 

Počet algoritmů, počet složení pro optimalizaci, technologie hyper parametry a počet sad hyperparametrické na čištění přes můžete také změnit v souboru Yaml pro rychlé spouštění modelů. Například, můžete spustit s menším počtem CV složení, menším počtem sady parametrů. Pokud je oprávněné, mohou také být spouštět více komplexněji s vyšší počet CV složení nebo větší počet sad parametrů.

Další informace najdete v tématu [automatizované modelování a vytváření sestav nástroje v TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Správa modelů
Po sestavené více modelů, obvykle potřebujete mít systém pro registraci a správu modelů. Obvykle budete potřebovat kombinaci skripty nebo rozhraní API a back-endové databáze nebo správu verzí systému. Několik možností, které mohou být užitečné pro tyto úlohy správy jsou:

1. [Azure Machine Learning – služba Správa modelů](../service/index.yml)
2. [ModelDB z MIT](https://mitdbg.github.io/modeldb/) 
3. [SQL server jako systém správy modelů](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a>nasazení

Produkční nasazení umožňuje modelu k přehrání aktivní roli v podniku. Předpovědi z nasazeného modelu lze použít pro obchodní rozhodnutí.

### <a name="production-platforms"></a>Produkčních platforem
Existují různé přístupy a platformy pro převedení modely do produkčního prostředí. Tady je několik možností:


- [Nasazení modelu ve službě Azure Machine Learning](../desktop-workbench/model-management-overview.md)
- [Nasazení modelu v systému SQL server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Před nasazením je nutné zajistit, že latence vyhodnocování modelu je dostatečně nízko, používat v produkčním prostředí.
>
>

Další příklady jsou k dispozici v postupy, které popisují všechny kroky v procesu pro **konkrétních scénářů**. Jsou uvedena v seznamu a propojené s Miniatura popisy v [příklad návody](walkthroughs.md) článku. Jejich ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací.

> [!NOTE]
> Nasazení pomocí Azure Machine Learning Studio, najdete v tématu [nasazení webové služby Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>A / B testování
Jsou-li několik modelů v produkčním prostředí, může být užitečné provést [A / B testování](https://en.wikipedia.org/wiki/A/B_testing) porovnat výkon modelů. 

 
## <a name="next-steps"></a>Další postup

[Sledovat průběh projekty datových věd](track-progress.md) ukazuje, jak mezi odborníky přes data, můžete sledovat průběh projektu datové vědy.

[Model operace a CI/CD](ci-cd-flask.md) ukazuje, jak lze provést CI/CD s vyvinuté modely.


