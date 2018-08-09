---
title: Skript Azure CLI – Změna konfigurace serveru
description: Tento ukázkový skript rozhraní příkazového řádku vypíše všechny dostupné konfigurace serveru a aktualizuje hodnotu innodb_lock_wait_timeout.
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
ms.openlocfilehash: 75b1228df8bc19ced1d8377768c08048e6ef9150
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441652"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Výpis a aktualizace konfigurace serveru Azure Database for MySQL pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku vypíše všechny dostupné parametry konfigurace a jejich povolené hodnoty pro server Azure Database for MySQL a nastaví parametr *innodb_lock_wait_timeout* na jinou než výchozí hodnotu.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Zkontrolujte verzi spuštěním příkazu `az --version`. Informace o instalaci nebo upgradu verze Azure CLI najdete v tématu [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript
V tomto ukázkovém skriptu upravte zvýrazněné řádky a aktualizujte uživatelské jméno a heslo správce na své vlastní.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=18-19 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění skriptu pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy uvedené v následující tabulce:

| **Příkaz** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az mysql server create](/cli/azure/mysql/server#az-msql-server-create) | Vytvoří server MySQL, který je hostitelem databází. |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-msql-server-configuration-list) | Vypíše konfiguraci serveru Azure Database for MySQL. |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-msql-server-configuration-set) | Aktualizuje konfiguraci serveru Azure Database for MySQL. |
| [az mysql server configuration show](/cli/azure/mysql/server/configuration#az-msql-server-configuration-show) | Zobrazí konfiguraci serveru Azure Database for MySQL. |
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace o Azure CLI najdete tady: [Dokumentace k Azure CLI](/cli/azure).
- Vyzkoušejte i další skripty: [Ukázky v Azure CLI pro službu Azure Database for MySQL](../sample-scripts-azure-cli.md)
- Další informace o parametrech serveru najdete v tématu [Konfigurace parametrů serveru ve službě Azure Database for MySQL](../howto-server-parameters.md).
