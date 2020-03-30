---
title: Provádění úloh datové vědy – proces vědecké správy týmových dat
description: Jak může datový vědec provést projekt datové vědy sledovatelným, řízeným verzí a kolaborativním způsobem.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e69a03cd142fdbcc5864ee38a4843e1c2e44a124
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477149"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Provádění úloh datové vědy: průzkum, modelování a nasazení

Typické úlohy datové vědy zahrnují zkoumání dat, modelování a nasazení. Tento článek ukazuje, jak pomocí **interaktivní data průzkum, analýzy a vykazování (IDEAR)** a **automatizované modelování a vykazování (AMAR)** nástroje k dokončení několika běžných úloh datové vědy, jako je interaktivní zkoumání dat, analýza dat, vytváření sestav a vytváření modelu. Možnosti nasazení modelu do produkčního prostředí mohou zahrnovat:

- [Azure Machine Learning](../index.yml)
- [SQL-Server se službami ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Server Microsoft Machine Learning](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> Průzkum 

Datový vědec může provádět průzkum a vykazování různými způsoby: pomocí knihoven a balíčků dostupných pro Python (například matplotlib) nebo s R (například ggplot nebo mříž). Vědci dat můžete přizpůsobit takový kód tak, aby vyhovoval potřebám zkoumání dat pro konkrétní scénáře. Potřeby pro zpracování strukturovaných dat se liší od potřeb nestrukturovaných dat, jako je text nebo obrázky. 

Produkty, jako je Azure Machine Learning, také poskytují [pokročilou přípravu dat](../how-to-create-register-datasets.md) pro hádání dat a zkoumání, včetně vytváření funkcí. Uživatel by měl rozhodnout o nástrojích, knihovnách a balíčcích, které nejlépe vyhovují svým potřebám. 

Výstupem na konci této fáze je zpráva o průzkumu dat. Zpráva by měla poskytnout poměrně komplexní přehled o údajích, které mají být použity pro modelování, a posouzení toho, zda jsou data vhodná k tomu, aby bylo možné přistoupit ke kroku modelování. Nástroje TDSP (Team Data Science Process) popsané v následujících částech pro poloautomatizovaný průzkum, modelování a vytváření sestav také poskytují standardizované sestavy průzkumu a modelování dat. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interaktivní zkoumání dat, analýza a vytváření sestav pomocí nástroje IDEAR

Tento nástroj založený na r markdownnebo Python na základě poznámkového bloku poskytuje flexibilní a interaktivní nástroj pro vyhodnocení a prozkoumání datových sad. Uživatelé mohou rychle generovat sestavy ze sady dat s minimálním kódováním. Uživatelé mohou klepnutím na tlačítka exportovat výsledky průzkumu v interaktivním nástroji do závěrečné sestavy, která může být doručena klientům nebo použita k rozhodování o tom, které proměnné mají být zahrnuty do následujícího kroku modelování.

V tomto okamžiku nástroj pracuje pouze na datových snímcích v paměti. Soubor YAML je potřeba určit parametry datové sady, které mají být prozkoumány. Další informace naleznete v tématu [IDEAR v tdsp data science utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> Modelování

Existuje mnoho sad nástrojů a balíčků pro modely školení v různých jazycích. Datoví vědci by měli klidně používat ty, které jsou pohodlné, pokud jsou splněny důležité informace o výkonu týkající se přesnosti a latence pro příslušné obchodní případy použití a produkční scénáře.

V další části je uvedeno, jak používat nástroj TDSP založený na R pro poloautomatické modelování. Tento nástroj AMAR lze použít ke generování modelů základní linie rychle, stejně jako parametry, které je třeba vyladit poskytnout výkonnější model.
Následující část správy modelu ukazuje, jak mít systém pro registraci a správu více modelů.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Model školení: modelování a vykazování pomocí nástroje AMAR

[Nástroj Automatické modelování a vytváření sestav (AMAR)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) poskytuje přizpůsobitelný poloautomatizovaný nástroj pro vytváření modelů s hyperparametrizací a pro porovnání přesnosti těchto modelů. 

Nástroj pro vytváření modelu je soubor R Markdown, který lze spustit za účelem vytvoření samostatného výstupu HTML s obsahem pro snadnou navigaci v různých částech. Tři algoritmy jsou spuštěny při spuštění souboru Markdown (knit): regularized regrese pomocí glmnet balíčku, náhodné doménové struktury pomocí randomForest balíček a posílení stromy pomocí xgboost balíčku). Každý z těchto algoritmů vytváří trénovaný model. Přesnost těchto modelů je pak porovnána a jsou hlášeny obrázky důležitosti relativnífunkce. V současné době existují dva nástroje: jeden je pro úlohu binární klasifikace a jeden je pro regresní úlohu. Hlavní rozdíly mezi nimi je způsob, jakým jsou určeny parametry řízení a přesnost metriky pro tyto úkoly učení. 

Soubor YAML se používá k určení:

- vstup dat (zdroj SQL nebo soubor R-Data) 
- jaká část dat se používá pro školení a jakou část pro testování
- které algoritmy spustit 
- volba řídicích parametrů pro optimalizaci modelu:
    - křížové ověření 
    - Bootstrapping
    - záhyby křížové validace
- hyperparametry pro každý algoritmus. 

Počet algoritmů, počet záhybů pro optimalizaci, hyperparametry a počet sad hyperparametrů, které mají být zamést, lze také upravit v souboru Yaml pro rychlé spuštění modelů. Například mohou být spuštěny s nižším počtem cv záhybů, nižším počtem sad parametrů. Pokud je to oprávněné, mohou být také provozovány komplexněji s vyšším počtem cv záhybů nebo větším počtem sad parametrů.

Další informace naleznete [v tématu Automated Modeling and Reporting Utility in TDSP Data Science Utilities](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Správa modelů
Po vytvoření více modelů obvykle potřebujete mít systém pro registraci a správu modelů. Obvykle potřebujete kombinaci skriptů nebo api a back-endové databáze nebo systému správy verzí. Několik možností, které můžete zvážit pro tyto úkoly správy jsou:

1. [Azure Machine Learning – služba pro správu modelů](../index.yml)
2. [ModelDB z MIT](http://modeldb.csail.mit.edu:3000/projects) 
3. [SQL-server jako systém pro správu modelů](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Server Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> Nasazení

Produkční nasazení umožňuje modelu hrát aktivní roli v podnikání. Předpovědi z nasazeného modelu lze použít pro obchodní rozhodnutí.

### <a name="production-platforms"></a>Výrobní plošiny
Existují různé přístupy a platformy, aby modely do výroby. Zde je několik možností:


- [Nasazení modelu v Azure Machine Learning](../how-to-deploy-and-where.md)
- [Nasazení modelu v SQL-serveru](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Server Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Před nasazením je třeba pojistit latence vyhodnocování modelu je dostatečně nízká pro použití v produkčním prostředí.
>
>

Další příklady jsou k dispozici v návodech, které ukazují všechny kroky v procesu pro **konkrétní scénáře**. Jsou uvedeny a propojeny s popisy miniatur v článku [ukázkové návody.](walkthroughs.md) Ilustrují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu a vytvořit tak inteligentní aplikaci.

> [!NOTE]
> Nasazení pomocí Azure Machine Learning Studio najdete [v tématu nasazení webové služby Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>A/B testování
Pokud je ve výrobě více modelů, může být užitečné provést [testování A/B](https://en.wikipedia.org/wiki/A/B_testing) za účelem porovnání výkonu modelů. 

 
## <a name="next-steps"></a>Další kroky

[Sledování průběhu projektů datové vědy](track-progress.md) ukazuje, jak může datový vědec sledovat průběh projektu datové vědy.

[Provoz modelu a CI/CD](ci-cd-flask.md) ukazuje, jak lze CI/CD provádět s vyvinutými modely.


