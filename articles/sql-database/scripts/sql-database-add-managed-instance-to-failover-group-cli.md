---
title: Příklad příkazového příkazu k selhání – skupina převzetí služeb při selhání – instance spravovaná azure SQL database
description: Ukázkový skript Azure CLI k vytvoření instance spravované azure SQL database, jeho přidání do skupiny převzetí služeb při selhání a testování převzetí služeb při selhání.
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
ms.openlocfilehash: 8ffe40662ffaf8a1fb35a3d31acfaea78ea0fbeb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061920"
---
# <a name="use-cli-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>Přidání spravované instance Azure SQL Database do skupiny převzetí služeb při selhání pomocí příkazového příkazu k přidání instance spravované službou Azure SQL Database

Tento příklad rozhraní příkazového příkazu k řešení Azure vytvoří dvě spravované instance, přidá je do skupiny převzetí služeb při selhání a pak testuje převzetí služeb při selhání z primární spravované instance na sekundární spravovanou instanci.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Spuštění skriptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní spojené prostředky. Skupinu prostředků budete muset odebrat dvakrát. První odebrání skupiny prostředků odebere spravované instance a virtuální clustery, `az group delete : Long running operation failed with status 'Conflict'.`ale poté se chybová zpráva nezdaří . Spusťte příkaz odstranění skupiny az podruhé, abyste odstranili všechny zbývající prostředky i skupinu prostředků.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Odkaz na vzorek

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| | |
|---|---|
| [síťová síť ová síť ová síť ová](/cli/azure/network/vnet) | Příkazy virtuální sítě.  |
| [podsíť síťové virtuální sítě AZ](/cli/azure/network/vnet/subnet) | Příkazy podsítě virtuální sítě. |
| [az síť nsg](/cli/azure/network/nsg) | Příkazy skupiny zabezpečení sítě. |
| [az síťová trasovací tabulka](/cli/azure/network/route-table) | Příkazy směrovací tabulky. |
| [az sql mi](/cli/azure/sql/mi) | Příkazy spravované instance. |
| [az síť public-ip](/cli/azure/network/public-ip) | Příkazy veřejných IP adres sítě. |
| [az síť virtuální síť-brána](/cli/azure/network/vnet-gateway) | Příkazy brány virtuální sítě. |
| [az sql instance-failover-group](/cli/azure/sql/instance-failover-group) | Příkazy skupiny převzetí služeb při selhání spravované instance. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../sql-database-cli-samples.md).
