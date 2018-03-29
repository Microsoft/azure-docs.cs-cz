---
title: Ukázkový skript Azure PowerShellu – Vytvoření clusteru Service Fabric | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Vytvoření testovacího clusteru Service Fabric se třemi uzly
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 03/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: a03362ebd4b8502f12b7c7bb9aadc558f6a073d2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-three-node-test-service-fabric-cluster"></a>Vytvoření testovacího clusteru Service Fabric se třemi uzly

Tento ukázkový skript vytvoří testovací cluster Service Fabric se třemi uzly zabezpečený pomocí certifikátu X.509. Konfigurace clusteru se třemi uzly je podporovaná pro vývoj a testování, protože můžete bezpečně provádět upgrady a překonat selhání jednotlivých uzlů (pokud k nim nedojde současně). Pro zajištění odolnosti proti současným selháním vyžaduje produkční cluster pět nebo více uzlů.  

Příkaz vytvoří certifikát podepsaný svým držitelem a nahraje ho do nového trezoru klíčů, který se vytvoří ve stejné skupině prostředků jako cluster. Certifikát se také zkopíruje do místního adresáře.  Nastavením parametru *-OS* zvolte verzi Windows nebo Linuxu, která se používá na uzlech clusteru.  Podle potřeby upravte parametry.

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním příkazu `Login-AzureRmAccount` vytvořte připojení k Azure. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-test-cluster/create-test-cluster.ps1 "Create a test Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků, cluster a všechny související prostředky.

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | Vytvoří nový cluster Service Fabric. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
