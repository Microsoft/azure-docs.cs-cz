---
title: Skript rozhraní příkazového řádku – stažení protokolů pomalých dotazů – Azure Database for MariaDB
description: Tento ukázkový skript Azure CLI ukazuje, jak povolit a stáhnout protokoly pomalých dotazů serveru Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 42b1806d3aa1235dfd976dd5700204aa0b9e4700
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785547"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Povolení a stažení protokolů pomalých dotazů serveru Azure Database for MariaDBového serveru pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku povolí a stáhne protokoly pomalých dotazů jednoho serveru Azure Database for MariaDB.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript
V tomto ukázkovém skriptu upravte zvýrazněné řádky a aktualizujte uživatelské jméno a heslo správce na své vlastní. &lt; &gt; V příkazech nahraďte Log_file_name `az monitor` vlastním názvem souboru protokolu serveru.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění skriptu pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy uvedené v následující tabulce:

| **Systému** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [AZ MariaDB Server Create](/cli/azure/mariadb/server#az_mariadb_server_create) | Vytvoří server MariaDB, který je hostitelem databází. |
| [AZ MariaDB Server Configuration list](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_list) | Vypíše hodnoty konfigurace serveru. |
| [AZ MariaDB Server Configuration set](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_set) | Aktualizuje konfiguraci serveru. |
| [AZ MariaDB Server-logs list](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_list) | Vypíše soubory protokolů serveru. |
| [AZ MariaDB Server-logs Download](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_download) | Stáhne soubory protokolů. |
| [az group delete](/cli/azure/group#az_group_delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace najdete v dokumentaci Azure CLI: [dokumentace k Azure CLI](/cli/azure).
- Vyzkoušejte další skripty: [ukázky v Azure CLI pro Azure Database for MariaDB](../sample-scripts-azure-cli.md)
