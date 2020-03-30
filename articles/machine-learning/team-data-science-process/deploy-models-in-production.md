---
title: Nasazení modelů v produkčním prostředí – proces vědecké ho zpracování týmových dat
description: Jak nasadit modely do produkčního prostředí, což jim umožňuje hrát aktivní roli při obchodních rozhodnutích.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e3e2ef3340ca836f56176c21cf3d221f0bf172b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722233"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Nasazení modelů do produkčního prostředí, abyste hráli aktivní roli při rozhodování o podnikání

Produkční nasazení umožňuje modelu hrát aktivní roli v podnikání. Předpovědi z nasazeného modelu lze použít pro obchodní rozhodnutí.

## <a name="production-platforms"></a>Výrobní plošiny

Existují různé přístupy a platformy, aby modely do výroby. Zde je několik možností:

- [Kde nasadit modely s Azure Machine Learning](../how-to-deploy-and-where.md)
- [Nasazení modelu v SQL-serveru](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Server Microsoft Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Před nasazením je třeba pojistit latence vyhodnocování modelu je dostatečně nízká pro použití v produkčním prostředí.
>

>[!NOTE]
>Nasazení pomocí Azure Machine Learning Studio najdete [v tématu nasazení webové služby Azure Machine Learning](../studio/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A/B testování

Pokud je ve výrobě více modelů, lze použít [testování A/B](https://en.wikipedia.org/wiki/A/B_testing) k porovnání výkonu modelu. 
 
## <a name="next-steps"></a>Další kroky

K dispozici jsou také návody, které ukazují všechny kroky v procesu pro **konkrétní scénáře.** Jsou uvedeny a propojeny s popisy miniatur v článku [ukázkové návody.](walkthroughs.md) Ilustrují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu a vytvořit tak inteligentní aplikaci. 
