---
title: 'Azure CLI: vytvoření spravované instance'
description: Ukázkový skript Azure CLI pro vytvoření spravované instance ve spravované instanci Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 54e1aa993f177a4d3bc255287ae8c2fb14cf65af
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87497269"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance"></a>Vytvoření spravované instance Azure SQL pomocí rozhraní příkazového řádku

Tento ukázkový skript Azure CLI vytvoří spravovanou instanci Azure SQL v vyhrazené podsíti v rámci nové virtuální sítě. Také nakonfiguruje směrovací tabulku a skupinu zabezpečení sítě pro virtuální síť. Po úspěšném spuštění skriptu je možné k spravované instanci přistupovat z virtuální sítě nebo z místního prostředí. Viz [konfigurace virtuálního počítače Azure pro připojení ke spravované instanci SQL Azure].. /.. /Azure-SQL/Managed-instance/Connect-VM-instance-configure.MD) a [nakonfigurujte připojení typu Point-to-site k spravované instanci Azure SQL z místního](../../azure-sql/managed-instance/point-to-site-p2s-configure.md)prostředí.

> [!IMPORTANT]
> Omezení najdete v tématu [podporované oblasti](../../azure-sql/managed-instance/resource-limits.md#supported-regions) a [podporované typy předplatného](../../azure-sql/managed-instance/resource-limits.md#supported-subscription-types).

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

| Příkaz | Popis |
|---|---|
| [AZ Network VNet](/cli/azure/network/vnet) | Příkazy virtuální sítě. |
| [AZ Network VNet Subnet](/cli/azure/network/vnet/subnet) | Příkazy podsítě virtuální sítě. |
| [AZ Network Route-Table](/cli/azure/network/route-table) | Příkazy tabulky směrování sítě. |
| [AZ SQL mi](/cli/azure/sql/mi) | Příkazy spravované instance SQL |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
