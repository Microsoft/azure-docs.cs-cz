---
title: Vyčištění samostatného clusteru
description: V tomto kurzu se naučíte vyčistit prostředky AWS nebo Azure v samostatném clusteru Service Fabric.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: bfb23ca5f5eb9540491fbd05efdfd6997db15e6b
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639016"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Kurz: Čištění samostatného clusteru

Samostatné clustery Service Fabric nabízejí možnost volby vlastního prostředí a vytvoření clusteru v rámci přístupu Service Fabric „jakýkoli operační systém a cloud“. V této sérii kurzů vytvoříte samostatný cluster hostovaný na AWS nebo Azure a nainstalujete do něj aplikaci.

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

5. Po zobrazení výzvy zadejte `Y`, pokud bylo úspěšné, váš výstup bude vypadat jako v následujícím seznamu s vašimi vlastními IP adresami, které jsou nahrazeny v:

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
4. Klikněte na **akce** > **stav instance** > **ukončit**.

## <a name="clean-up-azure-resources"></a>Vyčištění prostředků Azure

1. Přihlaste se k portálu Azure.
2. Přejít na část **Virtual Machines** .
3. Zaškrtněte políčka pro tři uzly, které jste vytvořili v první části kurzu.
4. Klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

Ve čtvrté části této série se naučíte čistit prostředky, které jste vytvořili v předchozích krocích.

> [!div class="checklist"]
> * Vyčištění prostředků

> [!div class="nextstepaction"]
> [Back Zpět na začátek](service-fabric-tutorial-standalone-create-infrastructure.md)
