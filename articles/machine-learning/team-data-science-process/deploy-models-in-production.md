---
title: Nasazení modelů v produkčním prostředí – Azure Machine Learning | Dokumentace Microsoftu
description: Jak nasadit modely do produkčního prostředí, což jim umožňuje přehrát aktivní roli při rozhodování o obchodní.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 5b1614f92f7633e008f4f7176723002dc7730b15
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495989"
---
# <a name="deploy-models-in-production"></a>Nasazení modelů v produkčním prostředí

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
