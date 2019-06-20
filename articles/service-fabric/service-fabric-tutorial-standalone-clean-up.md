---
title: Kurz čištění samostatného clusteru Service Fabric – Azure Service Fabric | Microsoft Docs
description: V tomto kurzu se dozvíte, jak vyčistit samostatného clusteru
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
ms.openlocfilehash: 9127ad1fe148f85779913454adf6578a9a8a1055
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274086"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Kurz: Vyčištění samostatného clusteru

Samostatné clustery Service Fabric nabízejí možnost volby vlastního prostředí a vytvoření clusteru v rámci přístupu Service Fabric „libovolný OS a libovolný cloud“. V této řadě kurzů vytvoření samostatného clusteru hostované na Azure nebo v AWS a instalovat aplikace do něj.

Tento kurz je čtvrtou částí série. Tuto část kurzu se dozvíte, jak vyčistit AWS nebo prostředky Azure, které jste vytvořili k hostování vašeho clusteru Service Fabric.

Ve čtvrté části této série se naučíte:

> [!div class="checklist"]
> * Čištění clusteru Service Fabric
> * Vyčistit AWS nebo prostředky Azure

## <a name="clean-up-service-fabric-cluster"></a>Čištění clusteru Service Fabric

1. Protokol RDP k virtuálnímu počítači, který jste použili k instalaci Service Fabric.
2. Otevřete PowerShell.
3. Změňte adresář na extrahovaný adresář z druhého kurzu.
4. Spuštěním následujícího příkazu odeberete cluster Service Fabric:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Zadejte `Y` po zobrazení výzvy, pokud byl úspěšný výstup bude vypadat jako následující okno, kde IP adresy nahrazena v:

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

1. Přihlaste se k vašemu účtu AWS.
2. Přejděte na konzolu EC2.
3. Vyberte tři uzly, které jste vytvořili v první části kurzu.
4. Klikněte na **akce** > **stav Instance** > **ukončit**.

## <a name="clean-up-azure-resources"></a>Vyčištění prostředků Azure

1. Přihlaste se k portálu Azure.
2. Přejděte **virtuálních počítačů** oddílu.
3. Zaškrtněte políčka pro tři uzly, které jste vytvořili v první části kurzu.
4. Klikněte na **odstranit**.

## <a name="next-steps"></a>Další postup

Ve čtvrté části této série se naučíte čistit prostředky, které jste vytvořili v předchozích krocích.

> [!div class="checklist"]
> * Vyčištění prostředků

> [!div class="nextstepaction"]
> [Back Zpět na začátek](service-fabric-tutorial-standalone-create-infrastructure.md)
