---
title: Kurz čištění samostatného clusteru Service Fabric – Azure Service Fabric | Microsoft Docs
description: V tomto kurzu se naučíte vyčistit samostatný cluster.
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
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bebe3a2dc83b651e713ee80d7b11068b13096e04
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385157"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Kurz: Vyčištění samostatného clusteru

Samostatné clustery Service Fabric nabízejí možnost volby vlastního prostředí a vytvoření clusteru v rámci přístupu Service Fabric „libovolný OS a libovolný cloud“. V této sérii kurzů vytvoříte samostatný cluster hostovaný na AWS nebo Azure a nainstalujete do něj aplikaci.

Tento kurz je čtvrtou částí série. V této části kurzu se dozvíte, jak vyčistit prostředky AWS nebo Azure, které jste vytvořili pro hostování Service Fabricho clusteru.

Ve čtvrté části této série se naučíte:

> [!div class="checklist"]
> * Čištění clusteru Service Fabric
> * Vyčištění prostředků AWS nebo Azure

## <a name="clean-up-service-fabric-cluster"></a>Čištění clusteru Service Fabric

1. Protokol RDP na virtuální počítač, který jste použili k instalaci Service Fabric.
2. Otevřete PowerShell.
3. Změňte adresář na extrahovaný adresář z druhého kurzu.
4. Spuštěním následujícího příkazu odeberete cluster Service Fabric:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Po `Y` zobrazení výzvy zadejte, že pokud bylo úspěšné, váš výstup bude vypadat jako následující s vašimi vlastními IP adresami, které jsou nahrazeny v:

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

1. Přihlaste se ke svému účtu AWS.
2. Přejděte na konzolu EC2.
3. Vyberte tři uzly, které jste vytvořili v první části kurzu.
4. Klikněte na položku **Akce** > **ukončit** **stav** > instance.

## <a name="clean-up-azure-resources"></a>Vyčištění prostředků Azure

1. Přihlaste se k portálu Azure.
2. Přejít na část **Virtual Machines** .
3. Zaškrtněte políčka pro tři uzly, které jste vytvořili v první části kurzu.
4. Klikněte na **Odstranit**.

## <a name="next-steps"></a>Další postup

Ve čtvrté části této série se naučíte čistit prostředky, které jste vytvořili v předchozích krocích.

> [!div class="checklist"]
> * Vyčištění prostředků

> [!div class="nextstepaction"]
> [Back Zpět na začátek](service-fabric-tutorial-standalone-create-infrastructure.md)
