---
title: Příklad rozhraní příkazového řádku – skupina převzetí služeb při selhání Azure SQL Database spravovaná instance
description: Ukázkový skript Azure CLI pro vytvoření spravované instance Azure SQL Database, její přidání do skupiny převzetí služeb při selhání a testovací převzetí služeb při selhání.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 792746ec3bfcf429afb7919458b9ac7ec8446b60
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061852"
---
# <a name="use-cli-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>Přidání spravované instance Azure SQL Database do skupiny převzetí služeb při selhání pomocí rozhraní příkazového řádku

Tento příklad rozhraní příkazového řádku Azure vytvoří dvě spravované instance, přidá je do skupiny převzetí služeb při selhání a pak otestuje převzetí služeb při selhání z primární spravované instance do sekundární spravované instance.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-scripts"></a>Ukázkové skripty

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Spuštění skriptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. Skupinu prostředků budete muset odebrat dvakrát. Když se skupina prostředků odebere poprvé, odebere se spravovaná instance a virtuální clustery, ale tato chybová zpráva `az group delete : Long running operation failed with status 'Conflict'.`se pak nezdaří. Pokud chcete odebrat všechny zbývající prostředky i skupinu prostředků, spusťte příkaz AZ Group DELETE a podruhé.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Vzorový odkaz

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| | |
|---|---|
| [AZ Network VNet](/cli/azure/network/vnet) | Příkazy virtuální sítě.  |
| [AZ Network VNet Subnet](/cli/azure/network/vnet/subnet) | Příkazy podsítě virtuální sítě. |
| [AZ Network NSG](/cli/azure/network/nsg) | Příkazy skupiny zabezpečení sítě. |
| [AZ Network NSG Rule](/cli/azure/network/nsg/rule)| Příkazy pravidla zabezpečení sítě. |
| [AZ Network Route-Table](/cli/azure/network/route-table) | Příkazy tabulky směrování |
| [AZ SQL mi](/cli/azure/sql/mi) | Příkazy spravované instance. |
| [AZ Network Public-IP](/cli/azure/network/public-ip) | Příkazy pro veřejnou IP adresu sítě. |
| [AZ Network VNet-Gateway](/cli/azure/network/vnet-gateway) | Příkazy Virtual Network brány |
| [AZ SQL instance-Failover-Group](/cli/azure/sql/instance-failover-group) | Příkazy skupiny pro převzetí služeb při selhání spravované instance. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../sql-database-cli-samples.md).
