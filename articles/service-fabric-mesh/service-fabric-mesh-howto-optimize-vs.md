---
title: Optimalizace sítě Visual Studio pro Azure Service Fabric
description: Tento článek ukazuje, jak optimalizovat výkon sady Visual Studio pro projekty sítě Service Fabric tak, aby vaše první ladění spustit (F5) je mnohem rychlejší.
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 40c4e9972a3d83a5dd8247bacac12e9d67a15f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497977"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Optimalizace výkonu visual studia pro projekty sítě Service Fabric Mesh

Tento článek ukazuje, jak optimalizovat výkon sady Visual Studio pro projekty sítě Service Fabric tak, aby vaše první ladění spustit (F5) je mnohem rychlejší.  

## <a name="change-visual-studio-settings"></a>Změna nastavení sady Visual Studio
 
V sadě Visual Studio můžete v části **Tools** > **Options**  > **Service Fabric Mesh Tools** > **General**upravit následující nastavení:

- **Vyžádat požadované image Dockeru při otevření projektu** urychlí váš první ladicí běh (F5) spuštěním procesu stahování bitové kopie při načítání projektu.  
- **Nasazení aplikace na otevření projektu** můžete provést první ladění spustit (F5) rychleji spuštěním procesu nasazení po otevření projektu.  
- **Odebrat aplikaci při zavření projektu uvolní** prostředky (CPU, RAM) přidělené do aplikace odebráním aplikace Mesh při zavření projektu.  

Když se zobrazí zprávy ve výstupním okně Nástroje service fabric, že Visual Studio je 'tahání obrázků', 'zahřívání' nebo 'odebrání aplikace', je v odkazu na výše uvedené nastavení. Tato nastavení můžete vypnout.

## <a name="next-steps"></a>Další kroky

Přečtěte si [kurz ladění aplikace Mesh](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)