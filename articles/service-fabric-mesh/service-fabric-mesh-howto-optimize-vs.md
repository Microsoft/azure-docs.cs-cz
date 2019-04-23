---
title: Optimalizace výkonu sady Visual Studio pro projekty Azure Service Fabric mřížky | Dokumentace Microsoftu
description: Optimalizace výkonu sady Visual Studio pro aplikace Azure Service Fabric mřížky
services: service-fabric-mesh
keywords: optimalizovat výkonnost ladění
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: f7a0cb47ad8010bd54a817e9990221b320cde541
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419046"
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