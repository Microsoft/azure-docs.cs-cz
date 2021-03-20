---
title: Příklad auditování a rozšířené ochrany před internetovými útoky – Azure SQL Database
description: Ukázkový skript Azure CLI pro konfiguraci auditování a rozšířené ochrany před internetovými útoky v Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security
ms.devlang: azurecli
ms.topic: sample
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/09/2021
ms.openlocfilehash: 7450911dabe04ea04ba16ec5e648f09ecf4a616f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100095983"
---
# <a name="use-cli-to-configure-sql-database-auditing-and-advanced-threat-protection"></a>Konfigurace auditování SQL Database a rozšířené ochrany před internetovými útoky pomocí rozhraní příkazového řádku

Tento ukázkový skript rozhraní příkazového řádku Azure Nakonfiguruje auditování SQL Database a rozšířenou ochranu před internetovými útoky.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Spuštění skriptu

```azurecli-interactive
#!/bin/bash
location="East US"
randomIdentifier=random123

resource="resource-$randomIdentifier"
server="server-$randomIdentifier"
database="database-$randomIdentifier"
storage="storage$randomIdentifier"

notification="changeto@your.email;changeto@your.email"

login="sampleLogin"
password="samplePassword123!"

echo "Using resource group $resource with login: $login, password: $password..."

echo "Creating $resource..."
az group create --name $resource --location "$location"

echo "Creating $server in $location..."
az sql server create --name $server --resource-group $resource --location "$location" --admin-user $login --admin-password $password

echo "Creating $database on $server..."
az sql db create --name $database --resource-group $resource --server $server --service-objective S0

echo "Creating $storage..."
az storage account create --name $storage --resource-group $resource --location "$location" --sku Standard_LRS

echo "Setting access policy on $storage..."
az sql db audit-policy update --resource-group $resource --server $server --name $database --state Enabled --blob-storage-target-state Enabled --storage-account $storage

echo "Setting threat detection policy on $storage..."
az sql db threat-policy update --email-account-admins Disabled --email-addresses $notification --name $database --resource-group $resource --server $server --state Enabled --storage-account $storage
```

### <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Vzorový odkaz

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Popis |
|---|---|
| [AZ SQL DB audit-Policy](/cli/azure/sql/db/audit-policy) | Nastaví pro databázi zásady auditu. |
| [AZ SQL DB Threat-Policy](/cli/azure/sql/db/threat-policy) | Nastaví pro databázi pokročilé zásady ochrany před internetovými útoky. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
