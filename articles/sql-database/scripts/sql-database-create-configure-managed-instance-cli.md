---
title: Příklad příkazového příkazového příkazu – vytvoření spravované instance v Azure SQL Database
description: Ukázkový skript Azure CLI pro vytvoření spravované instance v Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 998bd8e39863dd9520d1a05e7fff52095c3be6c1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067450"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>Vytvoření spravované instance Azure SQL Database pomocí příkazového příkazového příkazu

Tento příklad skriptu Azure CLI vytvoří instanci spravované službou Azure SQL Database ve vyhrazené podsíti v rámci nové virtuální sítě. Také konfiguruje směrovací tabulku a skupinu zabezpečení sítě pro virtuální síť. Po úspěšném spuštění skriptu lze ke spravované instanci přistupovat z virtuální sítě nebo z místního prostředí. Viz [Konfigurace virtuálního počítače Azure pro připojení ke spravované instanci Azure SQL Database A](../sql-database-managed-instance-configure-vm.md) konfigurace připojení z bodu na místo ke spravované [instanci Azure SQL Database z místního prostředí](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Omezení naleznete v tématu [podporované oblasti](../sql-database-managed-instance-resource-limits.md#supported-regions) a podporované [typy předplatného](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Spuštění skriptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní spojené prostředky.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Odkaz na vzorek

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| | |
|---|---|
| [síťová síť ová síť ová síť ová](/cli/azure/network/vnet) | Příkazy virtuální sítě. |
| [podsíť síťové virtuální sítě AZ](/cli/azure/network/vnet/subnet) | Příkazy podsítě virtuální sítě. |
| [az síťová trasovací tabulka](/cli/azure/network/route-table) | Příkazy tabulky síťových tras. |
| [az sql mi](/cli/azure/sql/mi) | Příkazy spravované instance. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../sql-database-cli-samples.md).
