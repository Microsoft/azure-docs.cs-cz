---
title: Skript příkazového příkazového příkazu – škálovací server – databáze Azure pro MariaDB
description: Tento ukázkový skript příkazového příkazového příkazu škáluje Azure Database pro server MariaDB na jinou úroveň výkonu po dotazování na metriky.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: 562f265cccf444740c177a41e516f9066188613e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74771631"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Monitorování a škálování databáze Azure pro server MariaDB pomocí azure cli
Tento ukázkový skript příkazového příkazového příkazu škáluje výpočetní prostředky a úložiště pro jeden azure databázi pro server MariaDB po dotazování na metriky. Výpočetní prostředky mohou vertikálně navýšit nebo snížit kapacitu. Úložiště může pouze vertikálně navýšit kapacitu.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Zkontrolujte verzi spuštěním příkazu `az --version`. Informace o instalaci nebo upgradu verze Azure CLI najdete v tématu [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript
Aktualizujte skript pomocí ID předplatného.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění skriptu pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy uvedené v následující tabulce:

| **Příkaz** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az mariadb server vytvořit](/cli/azure/mariadb/server#az-mariadb-server-create) | Vytvoří server MariaDB, který je hostitelem databází. |
| [aktualizace serveru az mariadb](/cli/azure/mariadb/server#az-mariadb-server-update) | Aktualizuje vlastnosti serveru MariaDB. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Vypíše hodnotu metriky pro prostředky. |
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace o [výpočetních a laurech Azure Database pro MariaDB](../concepts-pricing-tiers.md)
- Vyzkoušejte další skripty: [Ukázky Azure CLI pro Azure Database pro MariaDB](../sample-scripts-azure-cli.md)
- Další informace o [azure cli](/cli/azure)
