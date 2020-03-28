---
title: Skript rozhraní příkazového příkazového příkazu – stažení protokolů pomalých dotazů – databáze Azure pro MariaDB
description: Tato ukázková skript příkazového příkazu k webu Azure ukazuje, jak povolit a stáhnout protokoly pomalých dotazů serveru Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: a6d4ed70418f39b24a68362d5006f3f374693cb6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74777344"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Povolení a stahování protokolů pomalých dotazů serveru Azure database pro server MariaDB pomocí rozhraní příkazového příkazu Azure
Tento ukázkový skript rozhraní příkazového příkazového příkazu umožňuje a stahuje protokoly pomalých dotazů jedné databáze Azure pro server MariaDB.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Zkontrolujte verzi spuštěním příkazu `az --version`. Informace o instalaci nebo upgradu verze Azure CLI najdete v tématu [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript
V tomto ukázkovém skriptu upravte zvýrazněné řádky a aktualizujte uživatelské jméno a heslo správce na své vlastní. &lt;Nahraďte&gt; log_file_name `az monitor` v příkazech vlastním názvem souboru protokolu serveru.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění skriptu pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/server-logs/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy uvedené v následující tabulce:

| **Příkaz** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az mariadb server vytvořit](/cli/azure/mariadb/server#az-mariadb-server-create) | Vytvoří server MariaDB, který je hostitelem databází. |
| [Az mariadb seznam konfigurace serveru](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Vypíše hodnoty konfigurace serveru. |
| [Az mariadb server konfigurační sada](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Aktualizuje konfiguraci serveru. |
| [seznam protokolů serveru az mariadb](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) | Vypíše soubory protokolů serveru. |
| [az mariadb server-protokoly ke stažení](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) | Stáhne soubory protokolů. |
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o azure cli: [dokumentace KONTo IO Azure](/cli/azure).
- Vyzkoušejte další skripty: [Ukázky Azure CLI pro Azure Database pro MariaDB](../sample-scripts-azure-cli.md)
