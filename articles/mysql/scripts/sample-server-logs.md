---
title: Skript Azure CLI – Stažení protokolů serveru ve službě Azure Database for MySQL
description: Tento ukázkový skript Azure CLI ukazuje, jak povolit a stáhnout protokoly serveru Azure Database for MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: abe79185b817a4581c7df3f73c1d48ece6058643
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266250"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Povolení a stažení protokolů pomalých dotazů serveru Azure Database for MySQL pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku povolí a stáhne protokoly pomalých dotazů jednoho serveru Azure Database for MySQL.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Zkontrolujte verzi spuštěním příkazu `az --version`. Informace o instalaci nebo upgradu verze Azure CLI najdete v tématu [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript
V tomto ukázkovém skriptu upravte zvýrazněné řádky a aktualizujte uživatelské jméno a heslo správce na své vlastní. V příkazech `az monitor` nahraďte <log_file_name> vlastním názvem souboru protokolu serveru.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/server-logs.sh?highlight=18-19 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění skriptu pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy uvedené v následující tabulce:

| **Příkaz** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az mysql server create](/cli/azure/mysql/server#az_msql_server_create) | Vytvoří server MySQL, který je hostitelem databází. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) | Vypíše hodnoty konfigurace serveru. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) | Aktualizuje konfiguraci serveru. |
| [az mysql server-logs list](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) | Vypíše soubory protokolů serveru. |
| [az mysql server-logs download](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) | Stáhne soubory protokolů. |
| [az group delete](/cli/azure/group#az_group_delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace o Azure CLI najdete tady: [Dokumentace k Azure CLI](/cli/azure).
- Vyzkoušejte i další skripty: [Ukázky v Azure CLI pro službu Azure Database for MySQL](../sample-scripts-azure-cli.md)
