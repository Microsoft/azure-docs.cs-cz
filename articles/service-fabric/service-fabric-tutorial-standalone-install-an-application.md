---
title: Kurz instalace aplikace na samostatný cluster Service Fabric – Azure Service Fabric | Microsoft Docs
description: V tomto kurzu se dozvíte, jak chcete instalovat aplikace do samostatného clusteru Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 5bc326bbc16ef93d484425f26b6f8226150c77c6
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66302426"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Kurz: Nasazení aplikace na samostatný cluster Service Fabric

Samostatné clustery Service Fabric nabízejí možnost volby vlastního prostředí a vytvoření clusteru v rámci přístupu Service Fabric „libovolný OS a libovolný cloud“. V této řadě kurzů vytvoření samostatného clusteru hostovaných ve službě AWS a nasadit aplikaci do něj.

Tento kurz je třetí částí série.  Samostatné clustery Service Fabric nabízí možnost vybrat vlastní prostředí a vytvoření clusteru v rámci naší "Libovolný operační systém a všechny cloudy" přístup s využitím Service Fabric. V tomto kurzu se dozvíte, jak vytvořit infrastrukturu AWS potřebnou k hostování tohoto samostatného clusteru.

Ve třetí části této série se naučíte:

> [!div class="checklist"]
> * Stažení ukázkové aplikace
> * Nasazení do clusteru

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* [Nainstalovat Visual Studio 2019](https://www.visualstudio.com/) a nainstalujte **vývoj pro Azure** a **vývoj pro ASP.NET a web** úlohy.
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

## <a name="next-steps"></a>Další postup

Ve třetí části této série jste zjistili, jak nasadit aplikaci do clusteru:

> [!div class="checklist"]
> * Stažení ukázkové aplikace
> * Nasazení do clusteru

Přejděte ke čtvrté části série a vyčistěte cluster.

> [!div class="nextstepaction"]
> [Vyčištění prostředků](service-fabric-tutorial-standalone-clean-up.md)