---
title: Příklad příkazového příkazu– povolení byok TDE – instance spravovaná databáze Azure SQL
description: Zjistěte, jak nakonfigurovat spravovanou instanci Azure SQL tak, aby začala používat transparentní šifrování dat (TDE) pomocí prostředí PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: 8e8c0e2db1f87cca52c44d33ce14d7ce4f00e895
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061734"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Správa transparentního šifrování dat ve spravované instanci pomocí vlastního klíče z trezoru klíčů Azure

Tento příklad skriptu Azure CLI konfiguruje transparentní šifrování dat (TDE) s klíčem spravovaným zákazníkem pro Azure SQL Managed Instance pomocí klíče z Trezoru klíčů Azure. To se často označuje jako přineste si vlastní klíč scénář pro TDE. Další informace o TDE s klíčem spravovaným zákazníkem najdete v tématu [TDE Bring Your Own Key to Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

### <a name="prerequisites"></a>Požadavky

Existující spravovaná instance najdete v článku [Použití nastavení příkazového příkazu k Azure k vytvoření spravované instance Azure SQL Database](sql-database-create-configure-managed-instance-cli.md).

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Spuštění skriptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní spojené prostředky.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Odkaz na vzorek

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| | |
|---|---|
| [az SQL DB](/cli/azure/sql/db) | Příkazy databáze. |
| [az sql skupina převzetí služeb při selhání](/cli/azure/sql/failover-group) | Příkazy skupiny převzetí služeb při selhání. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../sql-database-cli-samples.md).
