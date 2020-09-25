---
title: Provádění úloh pro datové vědy – vědecký proces týmových dat
description: Jak může odborník na data spustit projekt pro datové vědy, který je možné sledovat, kontrolovat verze a jak spolupracovat.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 5fe11b20ff8e96bc4b0d997c49201aafdd2e2cac
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320519"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Provádění úloh pro datové vědy: průzkum, modelování a nasazení

Mezi typické úlohy pro datové vědy patří zkoumání, modelování a nasazení dat. V tomto článku se dozvíte, jak používat interaktivní nástroje pro **zkoumání a analýzu dat a vytváření sestav (** **AMAR)** k dokončení několika běžných úloh vědeckého zpracování dat, jako jsou interaktivní zkoumání dat, analýza dat, generování sestav a vytváření modelů. Možnosti nasazení modelu do produkčního prostředí můžou zahrnovat:

- [Azure Machine Learning](../index.yml)
- [SQL Server se službami ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> průzkum 

Odborník na data může provádět průzkum a vytváření sestav různými způsoby: pomocí knihoven a balíčků dostupných pro Python (například matplotlib) nebo pomocí R (například ggplot nebo mřížky). Vědečtí data mohou přizpůsobit takový kód tak, aby vyhovoval potřebám průzkumu dat pro konkrétní scénáře. Požadavky na práci se strukturovanými daty se liší v případě nestrukturovaných dat, jako je například text nebo obrázky. 

Produkty jako Azure Machine Learning také poskytují [pokročilou přípravu dat](../how-to-create-register-datasets.md) pro tahání a zkoumání dat, včetně vytváření funkcí. Uživatel by se měl rozhodnout o nástrojích, knihovnách a balíčcích, které nejlépe vyhovují jejich potřebám. 

Doručení na konci této fáze je sestava zkoumání dat. Zpráva by měla poskytnout poměrně komplexní pohled na data, která se mají použít k modelování, a posouzení, jestli jsou data vhodná pro pokračování v kroku modelování. Nástroje týmu pro vědecké zpracování dat (TDSP) popsané v následujících částech pro poloautomatické zkoumání, modelování a vytváření sestav také poskytují standardizované sestavy pro zkoumání a modelování dat. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interaktivní zkoumání dat, analýza a vytváření sestav pomocí nástroje myšlenku

Tento nástroj založený na poznámkovém bloku R Markdownu nebo Python poskytuje flexibilní a interaktivní nástroj pro vyhodnocení a prozkoumání datových sad. Uživatelé mohou rychle generovat sestavy z datové sady s minimálním kódováním. Uživatelé mohou kliknout na tlačítka a exportovat výsledky průzkumu v interaktivním nástroji do konečné sestavy, kterou lze doručit klientům nebo použít k rozhodnutí o tom, které proměnné mají být zahrnuty v následném kroku modelování.

V tuto chvíli nástroj funguje jenom pro datové rámce v paměti. K určení parametrů dat, které se má prozkoumat, se vyžaduje soubor YAML. Další informace najdete v tématu [nápad v TDSP pro datové vědy](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> modelování

K dispozici je řada nástrojů a balíčků pro školicí modely v různých jazycích. Odborníci na data by měli mít volnost v používání, které někdy jsou vhodné, pokud jsou splněny požadavky na výkon týkající se přesnosti a latence pro příslušné případy podnikového použití a produkční scénáře.

V další části se dozvíte, jak používat nástroj TDSP založený na jazyce R pro částečně automatizované modelování. Tento nástroj AMAR se dá použít ke snadnému vygenerování základních modelů a také k parametrům, které je potřeba vyladit, aby bylo možné model lépe provádět.
V následující části Správa modelů se dozvíte, jak mít systém pro registraci a správu více modelů.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Školení modelů: modelování a vytváření sestav pomocí nástroje AMAR

[Nástroj pro automatizované modelování a vytváření sestav (AMAR)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) nabízí přizpůsobitelný, poloautomatický Nástroj pro vytváření modelů s možností vyrovnávání pomocí technologie Hyper-v a pro porovnání přesnosti těchto modelů. 

Nástroj pro vytváření modelů je R Markdown soubor, který se dá spustit, aby se vytvořil vlastní výstup HTML s obsahem, který umožňuje snadnou navigaci prostřednictvím různých oddílů. Při spuštění souboru Markdownu (spojit) se spustí tři algoritmy: pravidelná regrese pomocí balíčku glmnet, náhodná doménová struktura pomocí balíčku randomForest a posílení stromů pomocí balíčku xgboost). Každý z těchto algoritmů vytváří trained model. Je pak porovnána přesnost těchto modelů a jsou hlášeny důležité funkce, které jsou k disměrnému prvku. V současné době existují dva pomůcky: jeden pro úlohu binární klasifikace a jeden pro regresní úlohu. Hlavním rozdílem mezi nimi je způsob, jakým jsou pro tyto úkoly učení zadány parametry ovládacího prvku a metriky přesnosti. 

Soubor YAML se používá k zadání:

- datový vstup (zdroj dat SQL nebo soubor R-data) 
- jaká část dat se používá pro školení a k čemu k testování
- které algoritmy se mají spustit 
- Volba řídicích parametrů pro optimalizaci modelu:
    - křížové ověření 
    - zavádění
    - skládání křížového ověřování
- sady parametrů technologie Hyper-v pro každý algoritmus. 

V souboru YAML je také možné upravit počet algoritmů, počet skládání pro optimalizaci, parametry technologie Hyper-v a počet sad parametrů technologie Hyper-v, aby se modely spouštěly rychle. Lze je například spustit s nižším počtem přeložení CV, což je nižší počet sad parametrů. Pokud je to oprávněné, mohou být také spuštěny společně s vyšším počtem přeložení CV nebo větším počtem sad parametrů.

Další informace najdete v tématu [automatizované modelování a nástroj pro vytváření sestav v TDSP nástrojů pro datové vědy](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Správa modelů
Po vytvoření více modelů je obvykle potřeba mít systém pro registraci a správu modelů. Obvykle potřebujete kombinaci skriptů nebo rozhraní API a back-end databáze nebo systému správy verzí. Pro tyto úlohy správy můžete zvážit několik možností:

1. [Azure Machine Learning – služba správy modelů](../index.yml)
2. [ModelDB od MIT](http://modeldb.csail.mit.edu:3000/projects) 
3. [SQL Server jako systém správy modelů](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> nasazení

Produkční nasazení umožňuje modelu hrát aktivní roli v podniku. Předpovědi z nasazeného modelu lze použít pro obchodní rozhodnutí.

### <a name="production-platforms"></a>Produkční platformy
Existují různé přístupy a platformy pro vložení modelů do produkčního prostředí. Tady je několik možností:


- [Nasazení modelu v Azure Machine Learning](../how-to-deploy-and-where.md)
- [Nasazení modelu v systému SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Před nasazením musí mít jeden z nich jistotu, že latence modelu je pro použití v produkčním prostředí dostatečně nízká.
>
>

Další příklady jsou k dispozici v návodech, které ukazují všechny kroky v procesu pro **konkrétní scénáře**. Jsou uvedeny a propojeny s popisy miniatur v článku [příklad návodu](walkthroughs.md) . Ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu a vytvořit tak inteligentní aplikaci.

> [!NOTE]
> Informace o nasazení pomocí Azure Machine Learning Studio najdete v tématu [nasazení webové služby Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>Testování A/B
Pokud je v produkčním prostředí více modelů, může být užitečné provést [testování a/B](https://en.wikipedia.org/wiki/A/B_testing) pro porovnání výkonu modelů. 

 
## <a name="next-steps"></a>Další kroky

[Sledování průběhu projektů pro datové vědy](track-progress.md) ukazuje, jak může vědecký pracovník dat sledovat průběh projektu pro datové vědy.

[Operace modelu a CI/CD](ci-cd-flask.md) ukazuje, jak se dají provádět CI/CD s využitím rozvinutých modelů.
