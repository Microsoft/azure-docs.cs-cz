---
title: Vyčištění samostatného clusteru
description: V tomto kurzu se dozvíte, jak vyčistit prostředky AWS nebo Azure ve vašem samostatném clusteru Service Fabric.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bfb23ca5f5eb9540491fbd05efdfd6997db15e6b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75639016"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Kurz: Čištění samostatného clusteru

Samostatné clustery Service Fabric nabízejí možnost volby vlastního prostředí a vytvoření clusteru v rámci přístupu Service Fabric „jakýkoli operační systém a cloud“. V této sérii kurzů vytvoříte samostatný cluster hostovaný v AWS nebo Azure a nainstalujete do něj aplikaci.

Tento kurz je čtvrtou částí série. Tato část kurzu ukazuje, jak vyčistit prostředky AWS nebo Azure, které jste vytvořili pro hostování clusteru Service Fabric.

Ve čtvrté části této série se naučíte:

> [!div class="checklist"]
> * Čištění clusteru Service Fabric
> * Vyčištění prostředků AWS nebo Azure

## <a name="clean-up-service-fabric-cluster"></a>Čištění clusteru Service Fabric

1. RDP do virtuálního virtuálního serveru, který jste použili k instalaci Service Fabric.
2. Otevřete PowerShell.
3. Změňte adresář na extrahovaný adresář z druhého kurzu.
4. Spuštěním následujícího příkazu odeberete cluster Service Fabric:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. Zadejte `Y` po zobrazení výzvy, pokud byl úspěšný, bude výstup vypadat takto, s vlastními IP adresami nahrazenými v:

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
4. Klikněte na **Akce** > **Instance State** > **Terminate**.

## <a name="clean-up-azure-resources"></a>Vyčištění prostředků Azure

1. Přihlaste se k portálu Azure.
2. Přejděte do části **Virtuální počítače.**
3. Zaškrtněte políčka u tří uzlů, které jste vytvořili v první části kurzu.
4. Klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

Ve čtvrté části této série se naučíte čistit prostředky, které jste vytvořili v předchozích krocích.

> [!div class="checklist"]
> * Vyčištění prostředků

> [!div class="nextstepaction"]
> [Back Zpět na začátek](service-fabric-tutorial-standalone-create-infrastructure.md)
