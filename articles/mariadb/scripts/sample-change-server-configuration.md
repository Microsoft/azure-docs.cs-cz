---
title: Skript rozhraní příkazového řádku – změna parametrů serveru – databáze Azure pro MariaDB
description: Tento ukázkový skript rozhraní příkazového řádku vypíše všechny dostupné konfigurace serveru a aktualizuje hodnotu innodb_lock_wait_timeout.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: 515eb7523c5a08d52ad5eb4f7bd261f3f4e03fc4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74771790"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Seznam a aktualizace konfigurace azure databáze pro MariaDB server pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku obsahuje seznam všech dostupných konfiguračních parametrů a jejich povolených hodnot pro Azure Database pro server MariaDB a nastaví *innodb_lock_wait_timeout* na hodnotu, která je jiná než výchozí.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Zkontrolujte verzi spuštěním příkazu `az --version`. Informace o instalaci nebo upgradu verze Azure CLI najdete v tématu [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

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
| [az mariadb server vytvořit](/cli/azure/mariadb/server#az-mariadb-server-create) | Vytvoří server MariaDB, který je hostitelem databází. |
| [Az mariadb seznam konfigurace serveru](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Seznam konfigurace databáze Azure pro server MariaDB. |
| [Az mariadb server konfigurační sada](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Aktualizujte konfiguraci databáze Azure pro server MariaDB. |
| [az mariadb server konfigurace show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | Zobrazit konfiguraci databáze Azure pro server MariaDB. |
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o azure cli: [dokumentace KONTo IO Azure](/cli/azure).
- Vyzkoušejte další skripty: [Ukázky Azure CLI pro Azure Database pro MariaDB](../sample-scripts-azure-cli.md)
- Další informace o parametrech serveru najdete [v tématu Konfigurace parametrů serveru v Azure Database for MariaDB](../howto-server-parameters.md).
