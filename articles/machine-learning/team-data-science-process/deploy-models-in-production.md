---
title: Nasazení modelů v produkčním procesu pro datové vědy týmu
description: Jak nasadit modely do produkčního prostředí, které jim umožní hrát aktivní roli při rozhodování o obchodních rozhodnutích.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6f371d07fdf09aae6ce4396f0916758f8588506b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73670310"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Nasazení modelů do produkčního prostředí za účelem přehrání aktivní role při rozhodování podnikových rozhodnutí

Produkční nasazení umožňuje modelu hrát aktivní roli v podniku. Předpovědi z nasazeného modelu lze použít pro obchodní rozhodnutí.

## <a name="production-platforms"></a>Produkční platformy

Existují různé přístupy a platformy pro vložení modelů do produkčního prostředí. Tady je několik možností:

- [Místo nasazení modelů pomocí Azure Machine Learning](../service/how-to-deploy-and-where.md)
- [Nasazení modelu v systému SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Před nasazením musí mít jeden z nich jistotu, že latence modelu je pro použití v produkčním prostředí dostatečně nízká.
>

>[!NOTE]
>Informace o nasazení pomocí Azure Machine Learning Studio najdete v tématu [nasazení webové služby Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>Testování a/B

Pokud je v produkčním prostředí více modelů, může být užitečné provést [testování a/B](https://en.wikipedia.org/wiki/A/B_testing) pro porovnání výkonu modelů. 
 
## <a name="next-steps"></a>Další kroky

K dispozici jsou také návody, které ukazují všechny kroky v procesu pro **konkrétní scénáře** . Jsou uvedeny a propojeny s popisy miniatur v článku [příklad návodu](walkthroughs.md) . Ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu a vytvořit tak inteligentní aplikaci. 
