---
title: Příklad rozhraní příkazového řádku – povolení BYOK TDE – spravovaná instance Azure SQL
description: Naučte se konfigurovat spravovanou instanci Azure SQL, abyste mohli začít používat BYOK transparentní šifrování dat (TDE) pro šifrování v klidovém prostředí pomocí PowerShellu.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto
ms.date: 11/05/2019
ms.openlocfilehash: 2b948161633569d629dfb048a7d7dee6a9946f43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323664"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Správa transparentní šifrování dat ve spravované instanci pomocí vlastního klíče z Azure Key Vault

Tento ukázkový skript Azure CLI nakonfiguruje transparentní šifrování dat (TDE) s klíčem spravovaným zákazníkem pro Azure SQL Managed instance pomocí klíče z Azure Key Vault. Tato situace se často označuje jako Bring Your Own Key scénář pro TDE. Další informace o TDE s klíčem spravovaným zákazníkem najdete v tématu [TDE Bring Your Own Key do Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md).

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

### <a name="prerequisites"></a>Požadavky

Existující spravovanou instanci najdete v tématu použití rozhraní příkazového [řádku Azure k vytvoření spravované instance Azure SQL](sql-database-create-configure-managed-instance-cli.md).

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Spuštění skriptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Vzorový odkaz

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Popis |
|---|---|
| [AZ SQL DB](/cli/azure/sql/db) | Příkazy databáze. |
| [AZ SQL Failover-Group](/cli/azure/sql/failover-group) | Příkazy skupiny převzetí služeb při selhání. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
