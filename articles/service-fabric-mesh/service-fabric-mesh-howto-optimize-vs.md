---
title: Optimalizace výkonu sady Visual Studio pro projekty Azure Service Fabric mřížky | Dokumentace Microsoftu
description: Optimalizace výkonu sady Visual Studio pro aplikace Azure Service Fabric mřížky
services: service-fabric-mesh
keywords: optimalizovat výkonnost ladění
author: tylermsft
ms.author: twhitney
ms.date: 11/29/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 72e900e6e48d18a721be7d2991428f81a81d1303
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52893497"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Optimalizace výkonu sady Visual Studio pro projekty služby prostředků infrastruktury sítě

Tento článek ukazuje, jak optimalizovat výkon sady Visual Studio pro projekty Service Fabric sítě tak, aby vaše první ladění (stisknutím klávesy F5) je mnohem rychlejší.  

## <a name="change-visual-studio-settings"></a>Změna nastavení sady Visual Studio
 
V sadě Visual Studio v části **nástroje** > **možnosti**  > **služby prostředků infrastruktury sítě nástroje** > **Obecné**, můžete upravit následující nastavení:

- **Získat požadované Image Dockeru v projektu otevřít** díky vaší první ladění (F5) rychleji spuštěním procesu stahování image při načítání projektu.  
- **Nasazení aplikace v projektu otevřít** může být vaší první ladění (F5) rychleji spuštěním procesu nasazení po otevření projektu.  
- **Odebrání aplikace na Zavřít projekt** uvolní prostředky (procesor, paměť RAM) přidělené aplikaci tak, že odeberete aplikaci sítě při zavření projektu.  

Když se zobrazí zprávy ve výstupním okně nástroje Service Fabric, který je Visual Studio "přetahování imagí", "zahřívání" nebo odebírání aplikace, je v souvislosti s aplikací výše uvedené nastavení. Tato nastavení můžete vypnout.

## <a name="next-steps"></a>Další postup

Přečtěte si [ladění aplikace kurzu mřížky](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)