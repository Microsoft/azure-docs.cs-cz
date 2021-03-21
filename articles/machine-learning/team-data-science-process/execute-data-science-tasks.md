---
title: Provádění úloh pro datové vědy – vědecký proces týmových dat
description: Jak může odborník na data spustit projekt pro datové vědy, který je možné sledovat, kontrolovat verze a jak spolupracovat.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e180ecbf5c68dbd9c179244083a641ac6ed42de0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100371953"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Provádění úloh pro datové vědy: průzkum, modelování a nasazení

Mezi typické úlohy pro datové vědy patří zkoumání, modelování a nasazení dat. Tento článek popisuje úlohy k dokončení několika běžných úkolů pro datové vědy, jako jsou interaktivní zkoumání dat, analýza dat, generování sestav a vytváření modelů. Možnosti nasazení modelu do produkčního prostředí můžou zahrnovat:

- [Azure Machine Learning](../index.yml)
- [SQL Server se službami ML](/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> průzkum 

Odborník na data může provádět průzkum a vytváření sestav různými způsoby: pomocí knihoven a balíčků dostupných pro Python (například matplotlib) nebo pomocí R (například ggplot nebo mřížky). Vědečtí data mohou přizpůsobit takový kód tak, aby vyhovoval potřebám průzkumu dat pro konkrétní scénáře. Požadavky na práci se strukturovanými daty se liší v případě nestrukturovaných dat, jako je například text nebo obrázky. 

Produkty jako Azure Machine Learning také poskytují [pokročilou přípravu dat](../how-to-create-register-datasets.md) pro tahání a zkoumání dat, včetně vytváření funkcí. Uživatel by se měl rozhodnout o nástrojích, knihovnách a balíčcích, které nejlépe vyhovují jejich potřebám. 

Doručení na konci této fáze je sestava zkoumání dat. Zpráva by měla poskytnout poměrně komplexní pohled na data, která se mají použít k modelování, a posouzení, jestli jsou data vhodná pro pokračování v kroku modelování. 

## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> modelování

K dispozici je řada nástrojů a balíčků pro školicí modely v různých jazycích. Odborníci na data by měli mít volnost v používání, které někdy jsou vhodné, pokud jsou splněny požadavky na výkon týkající se přesnosti a latence pro příslušné případy podnikového použití a produkční scénáře.

### <a name="model-management"></a>Správa modelů
Po vytvoření více modelů je obvykle potřeba mít systém pro registraci a správu modelů. Obvykle potřebujete kombinaci skriptů nebo rozhraní API a back-end databáze nebo systému správy verzí. Pro tyto úlohy správy můžete zvážit několik možností:

1. [Azure Machine Learning – služba správy modelů](../index.yml)
2. [ModelDB od MIT](https://people.csail.mit.edu/mvartak/papers/modeldb-hilda.pdf) 
3. [SQL Server jako systém správy modelů](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> nasazení

Produkční nasazení umožňuje modelu hrát aktivní roli v podniku. Předpovědi z nasazeného modelu lze použít pro obchodní rozhodnutí.

### <a name="production-platforms"></a>Produkční platformy
Existují různé přístupy a platformy pro vložení modelů do produkčního prostředí. Tady je několik možností:


- [Nasazení modelu v Azure Machine Learning](../how-to-deploy-and-where.md)
- [Nasazení modelu v systému SQL Server](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

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
