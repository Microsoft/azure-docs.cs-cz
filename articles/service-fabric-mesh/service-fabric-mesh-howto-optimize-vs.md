---
title: Optimalizace sady Visual Studio pro Azure Service Fabric sítě
description: V tomto článku se dozvíte, jak optimalizovat výkon sady Visual Studio pro Service Fabricch Mřížcích projektů tak, aby vaše první spuštění ladění (F5) bylo mnohem rychlejší.
author: georgewallace
ms.author: gwallace
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: e676286dca852c94f94e8bd8ff2ab73ee92b1412
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625748"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Optimalizace výkonu sady Visual Studio pro projekty Service Fabricch mřížek

> [!IMPORTANT]
> Náhled sítě Azure Service Fabric je vyřazený. Nová nasazení již nebudou povolena prostřednictvím rozhraní API pro Service Fabric sítě. Podpora stávajících nasazení bude pokračovat do 28. dubna 2021.
> 
> Podrobnosti najdete v tématu [vyřazení náhledu do sítě Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

V tomto článku se dozvíte, jak optimalizovat výkon sady Visual Studio pro Service Fabricch Mřížcích projektů tak, aby vaše první spuštění ladění (F5) bylo mnohem rychlejší.  

## <a name="change-visual-studio-settings"></a>Změnit nastavení sady Visual Studio
 
V aplikaci Visual Studio můžete v nabídce **nástroje**  >  **Možnosti**   >  **Service Fabric nástroje sítě**  >  **Obecné** upravit následující nastavení:

- **Vyžádané obrázky Docker v otevřeném projektu** usnadňují první spuštění ladění (F5) tím, že se spustí proces stahování obrazu během načítání projektu.  
- **Nasazení aplikace v otevřeném projektu** může urychlit první ladění (F5) tím, že se spustí proces nasazení po otevření projektu.  
- **Odebrat aplikaci v projektu zavřít** Redeklarace prostředků (CPU, RAM) přidělené aplikaci odebráním aplikace sítě, když je projekt uzavřený  

Když se zobrazí zprávy v okně Nástroje Service Fabric výstup, které Visual Studio používá k nastavování imagí, ohřívání nebo odebrání aplikace, je odkazem na výše uvedená nastavení. Tato nastavení můžete vypnout.

## <a name="next-steps"></a>Další kroky

Přečtěte si [kurz ladění aplikace pro mřížku](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)