---
title: 'Azure CLI: Přidání spravované instance do skupiny převzetí služeb při selhání'
description: Naučte se, jak vytvořit dvě spravované instance, přidat je do skupiny převzetí služeb při selhání a pak otestovat převzetí služeb při selhání.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: afefc556bcac096d67051f3014c31e449dbbca32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323562"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance-to-a-failover-group"></a>Vytvoření spravované instance Azure SQL pomocí rozhraní příkazového řádku pro skupinu převzetí služeb při selhání

Tento příklad rozhraní příkazového řádku Azure vytvoří dvě spravované instance, přidá je do skupiny převzetí služeb při selhání a pak otestuje převzetí služeb při selhání z primární spravované instance do sekundární spravované instance.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-scripts"></a>Ukázkové skripty

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Spuštění skriptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. Skupinu prostředků budete muset odebrat dvakrát. Když se skupina prostředků odebere poprvé, odebere se spravovaná instance a virtuální clustery, ale tato chybová zpráva se pak nezdaří `az group delete : Long running operation failed with status 'Conflict'.` . Pokud chcete odebrat všechny zbývající prostředky i skupinu prostředků, spusťte příkaz AZ Group DELETE a podruhé.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Vzorový odkaz

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Popis |
|---|---|
| [AZ Network VNet](/cli/azure/network/vnet) | Příkazy virtuální sítě.  |
| [AZ Network VNet Subnet](/cli/azure/network/vnet/subnet) | Příkazy podsítě virtuální sítě. |
| [AZ Network NSG](/cli/azure/network/nsg) | Příkazy skupiny zabezpečení sítě. |
| [AZ Network NSG Rule](/cli/azure/network/nsg/rule)| Příkazy pravidla zabezpečení sítě. |
| [AZ Network Route-Table](/cli/azure/network/route-table) | Příkazy tabulky směrování |
| [AZ SQL mi](/cli/azure/sql/mi) | Příkazy spravované instance SQL |
| [AZ Network Public-IP](/cli/azure/network/public-ip) | Příkazy pro veřejnou IP adresu sítě. |
| [AZ Network VNet-Gateway](/cli/azure/network/vnet-gateway) | Příkazy Virtual Network brány |
| [AZ SQL instance-Failover-Group](/cli/azure/sql/instance-failover-group) | Příkazy skupiny pro převzetí služeb při selhání spravované instance SQL |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
