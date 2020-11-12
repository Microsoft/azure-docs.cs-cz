---
title: Skript rozhraní příkazového řádku – Změna parametrů serveru – Azure Database for MariaDB
description: Tento ukázkový skript rozhraní příkazového řádku vypíše všechny dostupné konfigurace serveru a aktualizace Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: c7a46f98f74648ccae9f9f9f94c218d42056decb
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536801"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Seznam a aktualizace konfigurací Azure Database for MariaDB serveru pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku vypíše všechny dostupné parametry konfigurace, jakož i jejich přípustné hodnoty pro Azure Database for MariaDB Server a nastaví *innodb_lock_wait_timeout* na jinou hodnotu než výchozí.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript
V tomto ukázkovém skriptu upravte zvýrazněné řádky a aktualizujte uživatelské jméno a heslo správce na své vlastní.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění skriptu pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy uvedené v následující tabulce:

| **Příkaz** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [AZ MariaDB Server Create](/cli/azure/mariadb/server#az-mariadb-server-create) | Vytvoří server MariaDB, který je hostitelem databází. |
| [AZ MariaDB Server Configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Vypíše konfiguraci serveru Azure Database for MariaDB. |
| [AZ MariaDB Server Configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Aktualizujte konfiguraci serveru Azure Database for MariaDB. |
| [AZ MariaDB Server Configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | Zobrazit konfiguraci serveru Azure Database for MariaDB. |
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace najdete v dokumentaci Azure CLI: [dokumentace k Azure CLI](/cli/azure).
- Vyzkoušejte další skripty: [ukázky v Azure CLI pro Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Další informace o parametrech serveru najdete v tématu [Postup konfigurace parametrů serveru v Azure Database for MariaDB](../howto-server-parameters.md).
