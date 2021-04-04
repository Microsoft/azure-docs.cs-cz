---
title: Vyčištění samostatného clusteru
description: V tomto kurzu se dozvíte, jak odstranit prostředky AWS nebo Azure pro samostatný cluster Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 0d46e9068a311594f779411c3ccee2b408febb3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842882"
---
# <a name="tutorial-clean-up-your-standalone-cluster"></a>Kurz: Čištění samostatného clusteru

Service Fabric samostatné clustery nabízejí možnost zvolit si vlastní prostředí pro hostování Service Fabric. V této sérii kurzů vytvoříte samostatný cluster hostovaný na AWS nebo Azure a nasadíte do něj aplikaci.

Tento kurz je čtvrtou částí série. V této části kurzu se dozvíte, jak odstranit prostředky AWS nebo Azure, které jste vytvořili pro hostování Service Fabricho clusteru.

V tomto článku se naučíte:

> [!div class="checklist"]
> * Odebrat Cluster Service Fabric
> * Odstranění prostředků AWS nebo Azure

## <a name="remove-a-service-fabric-cluster"></a>Odebrat Cluster Service Fabric

1. Protokol RDP na virtuální počítač, který jste použili k instalaci Service Fabric.
2. Otevřete PowerShell.
3. Změňte adresář na extrahovaný adresář z druhého kurzu.
4. Spuštěním následujícího příkazu odeberete cluster Service Fabric:

  ```powershell
  .\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
  ```

5. `Y`Po zobrazení výzvy zadejte. Pokud bylo úspěšné, váš výstup bude vypadat jako následující (s vlastními IP adresami):

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

## <a name="delete-aws-resources"></a>Odstranění prostředků AWS

1. Přihlaste se ke svému účtu AWS.
2. Přejděte na konzolu EC2.
3. Vyberte tři uzly, které jste vytvořili v první části kurzu.
4. Vyberte **Akce**  >  **ukončit stav instance**  >  .

## <a name="delete-azure-resources"></a>Odstranění prostředků Azure

1. Přihlaste se k webu Azure Portal.
2. Přejít na část **Virtual Machines** .
3. Zaškrtněte políčka pro tři uzly, které jste vytvořili v první části kurzu.
4. Vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak odstranit prostředky, které jste vytvořili v předchozích krocích.

> [!div class="checklist"]
> * Vyčištění prostředků

> [!div class="nextstepaction"]
> [Back Zpět na začátek](service-fabric-tutorial-standalone-create-infrastructure.md)
