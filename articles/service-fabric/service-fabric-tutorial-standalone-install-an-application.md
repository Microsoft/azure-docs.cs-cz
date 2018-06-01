---
title: Kurz instalace aplikace na samostatný cluster Service Fabric – Azure Service Fabric | Microsoft Docs
description: V tomto kurzu se dozvíte, jak nainstalovat aplikaci do samostatného clusteru Service Fabric.
services: service-fabric
documentationcenter: .net
author: david-stanford
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dastanfo
ms.custom: mvc
ms.openlocfilehash: 65c0ca98393fbb4f54eee2f4864218f231765904
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208569"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Kurz: Nasazení aplikace do samostatného clusteru Service Fabric

Samostatné clustery Service Fabric nabízejí možnost volby vlastního prostředí a vytvoření clusteru v rámci přístupu Service Fabric „libovolný OS a libovolný cloud“. V této sérii kurzů vytvoříte samostatný cluster hostovaný na AWS a nasadíte do něj aplikaci.

Tento kurz je třetí částí série.  Samostatné clustery Service Fabric nabízejí možnost volby vlastního prostředí a vytvoření clusteru v rámci přístupu „libovolný OS a libovolný cloud“ pomocí Service Fabric. V tomto kurzu se dozvíte, jak vytvořit infrastrukturu AWS potřebnou k hostování tohoto samostatného clusteru.

Ve třetí části této série se naučíte:

> [!div class="checklist"]
> * Stažení ukázkové aplikace
> * Nasazení do clusteru

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* [Nainstalujte sadu Visual Studio 2017](https://www.visualstudio.com/) se sadami funkcí **Vývoj pro Azure** a **Vývoj pro ASP.NET a web**.
* [Nainstalujte sadu Service Fabric SDK](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Stažení ukázkové hlasovací aplikace

V příkazovém okně naklonujte spuštěním následujícího příkazu úložiště ukázkové aplikace do místního počítače.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Nasazení aplikace do clusteru Service Fabric

Jakmile je aplikace stažená, můžete ji do clusteru nasadit přímo ze sady Visual Studio.

1. Otevřete sadu Visual Studio.

2. Vyberte **Soubor** > **Otevřít**.

3. Přejděte do složky, do které jste naklonovali úložiště git, a vyberte Voting.sln.

4. V Průzkumníku řešení klikněte pravým tlačítkem na projekt aplikace `Voting` a zvolte **Publikovat**.

5. Vyberte rozevírací seznam pro **Koncový bod připojení** a zadejte veřejný název DNS jednoho z uzlů v clusteru.  Například `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`.

6. Otevřete váš preferovaný prohlížeč a zadejte adresu clusteru (koncový bod připojení, tato aplikace se nasadí na port 8080 – například ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![Odpověď rozhraní API z clusteru](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Další kroky

Ve třetí části této série jste zjistili, jak nasadit aplikaci do clusteru:

> [!div class="checklist"]
> * Stažení ukázkové aplikace
> * Nasazení do clusteru

Přejděte ke čtvrté části série a vyčistěte cluster.

> [!div class="nextstepaction"]
> [Vyčištění prostředků](service-fabric-tutorial-standalone-clean-up.md)