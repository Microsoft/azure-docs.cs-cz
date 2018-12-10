---
title: Nasazení modelů v produkčním prostředí – vědecké zpracování týmových dat
description: Jak nasadit modely do produkčního prostředí, což jim umožňuje přehrát aktivní roli při rozhodování o obchodní.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 35f9f369e752fa7c86a6bd295a79b79b23104d23
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53137867"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Nasadit modely do produkčního prostředí aktivní roli při obchodním rozhodování

Produkční nasazení umožňuje modelu k přehrání aktivní roli v podniku. Předpovědi z nasazeného modelu lze použít pro obchodní rozhodnutí.

## <a name="production-platforms"></a>Produkčních platforem

Existují různé přístupy a platformy pro převedení modely do produkčního prostředí. Tady je několik možností:

- [Kde nasazujte modely pomocí služby Azure Machine Learning](../service/how-to-deploy-and-where.md)
- [Nasazení modelu v systému SQL server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Před nasazením je nutné zajistit, že latence vyhodnocování modelu je dostatečně nízko, používat v produkčním prostředí.
>

>[!NOTE]
>Nasazení pomocí Azure Machine Learning Studio, najdete v tématu [nasazení webové služby Azure Machine Learning](../studio/publish-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A / B testování

Jsou-li několik modelů v produkčním prostředí, může být užitečné provést [A / B testování](https://en.wikipedia.org/wiki/A/B_testing) porovnat výkon modelů. 
 
## <a name="next-steps"></a>Další postup

Postupy, které popisují všechny kroky v procesu pro **konkrétních scénářů** jsou také k dispozici. Jsou uvedena v seznamu a propojené s Miniatura popisy v [příklad návody](walkthroughs.md) článku. Jejich ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 
