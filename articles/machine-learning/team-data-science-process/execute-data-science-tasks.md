---
title: Spuštění úlohy vědecké účely dat – Azure Machine Learning | Microsoft Docs
description: Jak vědecký pracovník dat můžete spustit projekt vědecké účely data v organizovaným, verze kontrolovat a způsob spolupráce.
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
ms.date: 11/28/2017
ms.author: deguhath
ms.openlocfilehash: 5c617c60ff51d0b1e7717b28b0372efe63c1ee18
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Spuštění úlohy vědecké účely dat: zkoumání, modelování a nasazení

Úlohy vědecké účely typické dat zahrnují zkoumání dat, modelování a nasazení. Tento článek ukazuje, jak používat **interaktivní zkoumání dat, analýzu a vytváření sestav (IDEAR)** a **automatizované modelování a vytváření sestav (AMAR)** nástrojů k dokončení několik běžných úloh dat vědecké účely například interaktivní zkoumání dat, analýzy dat, vytváření sestav a vytváření modelu. Popisuje také možnosti nasazení modelu do provozního prostředí pomocí různých platforem sadách a data, například následující:

- [Azure Machine Learning](../service/index.yml)
- [SQL Server službou ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services#in-database-analytics-with-sql-server)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> Zkoumání 

Vědecký pracovník dat můžete provádět zkoumání a vytváření sestav v mnoha různými způsoby: pomocí knihoven nebo balíčků, které jsou k dispozici pro jazyk Python (například matplotlib) nebo s R (ggplot nebo mřížky, např.). Datových vědců můžete přizpůsobit podle potřeby zkoumání dat u konkrétních scénářů takový kód. Požadavky pro práci s strukturovaných dat se liší, pro Nestrukturovaná data, jako je například textu nebo obrázků. 

Produkty, jako je Azure Machine Learning Workbench také obsahují [Rozšířená data přípravy](../desktop-workbench/tutorial-bikeshare-dataprep.md) pro data wrangling a zkoumání, včetně vytváření funkce. Uživatel se měli rozhodnout na nástroje, knihovny a balíčky, které osvědčené suite svých potřeb. 

Dodávky na konci této fáze je zkoumání dat sestavy. Sestavy by měl poskytovat poměrně rozsáhlé zobrazení data, která se použije pro modelování a jestli je vhodné pro pokračujte krokem modelování dat hodnocení. Nástroje Team datové vědy procesu (TDSP) popsané v následujících částech pro zkoumání poloautomatických, modelování a vytváření sestav také poskytují zkoumání standardizované dat a modelování sestavy. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interaktivní zkoumání dat, analýzu a vytváření sestav pomocí nástroje IDEAR

Tento na základě markdownu R nebo Python na základě poznámkového bloku nástroj poskytuje flexibilní a interaktivní nástroj k vyhodnocení a prozkoumejte datových sad. Uživatele můžete rychle vytvořit sestavy z datové sady s minimální kódování. Uživatelé mohou klepnutím na tlačítko Exportovat výsledky zkoumání v nástroji pro interaktivní do konečné zprávu, která lze doručit do klientů nebo použít rozhodnout o které proměnné, které mají být zahrnuty v kroku následné modelování.

V tomto okamžiku nástroj funguje pouze v datových rámců v paměti. Soubor YAML je potřeba zadat parametry sadu dat má být zkoumána. Další informace najdete v tématu [IDEAR v TDSP datové vědy nástroje](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a> Modelování

Existuje řada sad nástrojů a balíčky pro modely školení v různých jazycích. Datových vědců by měl klidně použití, která ever ty jsou nevyhovuje, tak dlouho, dokud pro příslušný obchodní byly splněny faktory ovlivňující výkon, pokud jde o přesnost a latence použít případy a produkčních scénářích.

V další části ukazuje, jak pomocí nástroje základě R TDSP pro poloautomatických modelování. Tento nástroj AMAR slouží ke generování modelů základní řádku rychle stejně jako parametry, které je potřeba zajistit lepší provádění ladit modelu.
V následující části model správy ukazuje, jak mít systém pro registraci a správu více modelů.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Model školení: modelování a vytváření sestav pomocí nástroje AMAR

[Automatizované modelování a nástroj pro vytváření sestav (AMAR)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) poskytuje přizpůsobitelné, poloautomatických nástroj k vytvoření modelu s hyper parametr (vymetání) komínů a k porovnání přesnost těchto modelů. 

Nástroj pro vytvoření modelu je soubor Markdownu R, který lze spustit a vytvořit samostatný výstupu protokolu HTML s obsah easy navigace prostřednictvím jednotlivých částech. Tři algoritmy jsou provést při spuštění (knit) souboru Markdownu: Vyřešeno regrese pomocí glmnet balíčku, náhodné doménové struktury pomocí balíčku randomForest a zvyšovat skóre stromy pomocí balíčku xgboost). Všechny tyto algoritmy vytvoří modulu trained model. Přesnost tyto modely se pak porovná a funkce relativní důležitost pozemků jsou hlášeny. V současné době existují dva nástroje: jeden je pro úlohu binární klasifikace a jeden pro úlohu regrese. Primární rozdíly mezi nimi je způsob řízení parametry a metriky přesnost jsou určené pro tyto úlohy učení. 

Soubor YAML slouží k určení:

- vstupní data (zdroje SQL nebo R datového souboru) 
- jaká část dat se používá pro školení a jaká část pro testování
- které algoritmy ke spuštění 
- Výběr parametrů pro ovládací prvek pro optimalizaci modelu:
    - křížové ověření 
    - zavedení spouštěcího programu
    - složení křížové ověření
- technologie hyper parametr nastaví pro každý algoritmus. 

Počet algoritmů, počet složení pro optimalizaci, technologie hyper parametry a počet sad hyper parametrů oblouku přes můžete také upravit v souboru Yaml ke spuštění modely rychle. Například se může běžet s nižší počet složení odchylka nákladů, používá menší počet sady parametrů. Pokud je oprávněná se lze také spustit více komplexněji s vyšší počet složení odchylka nákladů nebo větší počet sady parametrů.

Další informace najdete v tématu [automatizované modelování a vytváření sestav nástroje v TDSP datové vědy nástroje](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Správa modelů
Po které se sestavily více modelů, obvykle potřebujete mít systém pro registraci a správu modelů. Obvykle je nutné kombinaci skripty nebo rozhraní API a back-end databáze nebo Správa verzí systému. Několik možností, které mohou být užitečné pro tyto úlohy správy jsou:

1. [Azure Machine Learning - modelu služby správy](../service/index.yml)
2. [ModelDB od MIT](https://mitdbg.github.io/modeldb/) 
3. [SQL seerver jako systém správy modelu](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a>nasazení

Produkční nasazení umožňuje modelu pro hraje aktivní roli v podniku. Predikce z nasazené modelu lze použít pro obchodních rozhodnutí.

### <a name="production-platforms"></a>Produkční platformy
Existují různé přístupy a platformy pro modely uvedení do produkčního prostředí. Tady je několik možností:


- [Model nasazení v nástroji Azure Machine Learning](../desktop-workbench/model-management-overview.md)
- [Nasazení modelu v systému SQL server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>
>
>Poznámka: Před nasazením, jeden má zajistit, že je latence modelu vyhodnocování nízké používat v produkčním prostředí.
>

Další příklady jsou k dispozici v návody, které ukazují všechny kroky v procesu pro **konkrétních scénářů**. Jsou uvedena v seznamu a propojené s miniatur popisy v [příklad návody](walkthroughs.md) článku. Se ukazují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu vytvoření inteligentního aplikace.

Poznámka: Pro nasazení pomocí Azure Machine Learning Studio, najdete v části [nasazení webové služby Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).

### <a name="ab-testing"></a>A / B testování
Když více modelů v produkčním prostředí, může být užitečné k provedení [A / B testování](https://en.wikipedia.org/wiki/A/B_testing) k porovnání výkonu modelů. 

 
## <a name="next-steps"></a>Další postup

[Sledovat průběh datové vědy projekty](track-progress.md) ukazuje, jak vědecký pracovník dat můžete sledovat průběh projektu vědecké účely data.
 


