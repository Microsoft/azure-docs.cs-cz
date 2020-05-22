---
title: Příklad rozhraní příkazového řádku – vytvoření spravované instance v Azure SQL Database
description: Ukázkový skript Azure CLI pro vytvoření spravované instance v Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: f52a460be33b23da3da0cb32075ab5f75bb8d129
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772620"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>Vytvoření spravované instance Azure SQL Database pomocí rozhraní příkazového řádku

Tento ukázkový skript Azure CLI vytvoří Azure SQL Database spravovanou instanci ve vyhrazené podsíti v rámci nové virtuální sítě. Také nakonfiguruje směrovací tabulku a skupinu zabezpečení sítě pro virtuální síť. Po úspěšném spuštění skriptu je možné k spravované instanci přistupovat z virtuální sítě nebo z místního prostředí. Přečtěte si téma [Konfigurace virtuálního počítače Azure pro připojení k spravované instanci Azure SQL Database](../sql-database-managed-instance-configure-vm.md) a [Konfigurace připojení typu Point-to-site k Azure SQL Database spravované instanci z místního prostředí](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Omezení najdete v tématu [podporované oblasti](../sql-database-managed-instance-resource-limits.md#supported-regions) a [podporované typy předplatného](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Spuštění skriptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Vzorový odkaz

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| | |
|---|---|
| [AZ Network VNet](/cli/azure/network/vnet) | Příkazy virtuální sítě. |
| [AZ Network VNet Subnet](/cli/azure/network/vnet/subnet) | Příkazy podsítě virtuální sítě. |
| [AZ Network Route-Table](/cli/azure/network/route-table) | Příkazy tabulky směrování sítě. |
| [AZ SQL mi](/cli/azure/sql/mi) | Příkazy spravované instance. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../sql-database-cli-samples.md).
