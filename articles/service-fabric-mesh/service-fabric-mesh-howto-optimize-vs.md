---
title: Optimalizace sady Visual Studio pro Azure Service Fabric sítě
description: V tomto článku se dozvíte, jak optimalizovat výkon sady Visual Studio pro Service Fabricch Mřížcích projektů tak, aby vaše první spuštění ladění (F5) bylo mnohem rychlejší.
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 40c4e9972a3d83a5dd8247bacac12e9d67a15f66
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497977"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Optimalizace výkonu sady Visual Studio pro projekty Service Fabricch mřížek

V tomto článku se dozvíte, jak optimalizovat výkon sady Visual Studio pro Service Fabricch Mřížcích projektů tak, aby vaše první spuštění ladění (F5) bylo mnohem rychlejší.  

## <a name="change-visual-studio-settings"></a>Změnit nastavení sady Visual Studio
 
V aplikaci Visual Studio v části **nástroje** > **Možnosti**  > **Service Fabric nástroje sítě** > **Obecné**můžete upravit následující nastavení:

- **Vyžádané obrázky Docker v otevřeném projektu** usnadňují první spuštění ladění (F5) tím, že se spustí proces stahování obrazu během načítání projektu.  
- **Nasazení aplikace v otevřeném projektu** může urychlit první ladění (F5) tím, že se spustí proces nasazení po otevření projektu.  
- **Odebrat aplikaci v projektu zavřít** Redeklarace prostředků (CPU, RAM) přidělené aplikaci odebráním aplikace sítě, když je projekt uzavřený  

Když se zobrazí zprávy v okně Nástroje Service Fabric výstup, které Visual Studio používá k nastavování imagí, ohřívání nebo odebrání aplikace, je odkazem na výše uvedená nastavení. Tato nastavení můžete vypnout.

## <a name="next-steps"></a>Další kroky

Přečtěte si [kurz ladění aplikace pro mřížku](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)