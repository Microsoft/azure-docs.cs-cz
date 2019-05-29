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
ms.openlocfilehash: 67334a0e8ae3e6dcca86830cd088e6e446331aee
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306717"
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

* Přihlaste se k vašemu účtu AWS
* Přejděte na konzolu EC2.
* Vyberte tři uzly, které jste vytvořili v první části kurzu.
* Klikněte na **Akce** > **Stav instance** > **Ukončit**

## <a name="next-steps"></a>Další postup

Ve čtvrté části této série se naučíte čistit prostředky, které jste vytvořili v předchozích krocích.

> [!div class="checklist"]
> * Vyčištění prostředků

> [!div class="nextstepaction"]
> [Back Zpět na začátek](service-fabric-tutorial-standalone-create-infrastructure.md)