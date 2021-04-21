---
title: Skript rozhraní příkazového řádku – Změna parametrů serveru – Azure Database for MySQL
description: Tento ukázkový skript rozhraní příkazového řádku vypíše všechny dostupné konfigurace serveru a aktualizuje hodnotu innodb_lock_wait_timeout.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: eea11eb79cda47a11456178100941db05f0f2fa1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791655"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Výpis a aktualizace konfigurace serveru Azure Database for MySQL pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku vypíše všechny dostupné parametry konfigurace a jejich povolené hodnoty pro server Azure Database for MySQL a nastaví parametr *innodb_lock_wait_timeout* na jinou než výchozí hodnotu.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná. 

## <a name="sample-script"></a>Ukázkový skript
V tomto ukázkovém skriptu upravte zvýrazněné řádky a aktualizujte uživatelské jméno a heslo správce na své vlastní.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění skriptu pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy uvedené v následující tabulce:

| **Systému** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | Vytvoří server MySQL, který je hostitelem databází. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) | Vypíše konfiguraci serveru Azure Database for MySQL. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) | Aktualizuje konfiguraci serveru Azure Database for MySQL. |
| [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show) | Zobrazí konfiguraci serveru Azure Database for MySQL. |
| [az group delete](/cli/azure/group#az_group_delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace najdete v dokumentaci Azure CLI: [dokumentace k Azure CLI](/cli/azure).
- Vyzkoušejte i další skripty: [Ukázky v Azure CLI pro službu Azure Database for MySQL](../sample-scripts-azure-cli.md)
- Další informace o parametrech serveru najdete v tématu [Konfigurace parametrů serveru ve službě Azure Database for MySQL](../howto-server-parameters.md).
