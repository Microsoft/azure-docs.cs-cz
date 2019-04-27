---
title: Kurz čištění samostatného clusteru Service Fabric – Azure Service Fabric | Microsoft Docs
description: V tomto kurzu se naučíte čistit samostatný cluster
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
ms.openlocfilehash: 28cc2bf4794620641fb6af46bd4017d74f87e955
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60717998"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Kurz: Vyčištění samostatného clusteru

Samostatné clustery Service Fabric nabízejí možnost volby vlastního prostředí a vytvoření clusteru v rámci přístupu Service Fabric „libovolný OS a libovolný cloud“. V této sérii kurzů vytvoříte samostatný cluster hostovaný na AWS a nainstalujete do něj aplikaci.

Tento kurz je čtvrtou částí série. Tato část kurzu popisuje způsob čištění prostředků AWS vytvořených pro hostování vašeho clusteru Service Fabric.

Ve čtvrté části této série se naučíte:

> [!div class="checklist"]
> * Čištění clusteru Service Fabric
> * Čištění prostředků AWS

## <a name="clean-up-service-fabric-cluster"></a>Čištění clusteru Service Fabric

* Protokol RDP v instanci EC2, kterou jste použili k instalaci Service Fabric
* Otevření Powershellu
* Změňte adresář na extrahovaný adresář z druhého kurzu.
* Spuštěním následujícího příkazu odeberete cluster Service Fabric:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

* Při úspěšném zadání `Y` bude váš výstup vypadat následovně s nahrazenou IP adresou:

```powershell
Best Practices Analyzer completed successfully.
Removing configuration from machine 172.31.21.141
Removing configuration from machine 172.31.27.1
Removing configuration from machine 172.31.20.163
Configuration removed from machine 172.31.21.141
Configuration removed from machine 172.31.27.1
Configuration removed from machine 172.31.20.163
The cluster is successfully removed.
```

## <a name="clean-up-aws-resources"></a>Čištění prostředků AWS

* Přihlášení k účtu AWS
* Přejděte na konzolu EC2.
* Vyberte tři uzly, které jste vytvořili v první části kurzu.
* Klikněte na **Akce** > **Stav instance** > **Ukončit**

## <a name="next-steps"></a>Další postup

Ve čtvrté části této série se naučíte čistit prostředky, které jste vytvořili v předchozích krocích.

> [!div class="checklist"]
> * Vyčištění prostředků

> [!div class="nextstepaction"]
> [Back Zpět na začátek](service-fabric-tutorial-standalone-create-infrastructure.md)