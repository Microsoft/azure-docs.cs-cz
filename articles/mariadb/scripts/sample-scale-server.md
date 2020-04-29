---
title: Skript rozhraní příkazového řádku – škálování serveru – Azure Database for MariaDB
description: Tento ukázkový skript rozhraní příkazového řádku po dotazování metrik škáluje Azure Database for MariaDB Server na jinou úroveň výkonu.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 12/02/2019
ms.openlocfilehash: 562f265cccf444740c177a41e516f9066188613e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "74771631"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Monitorování a škálování serveru Azure Database for MariaDB pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku škáluje výpočetní výkon a úložiště pro jeden Azure Database for MariaDB Server po dotazování metrik. Výpočetní výkon se může škálovat nahoru nebo dolů. Velikost úložiště se dá škálovat jenom nahoru.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Zkontrolujte verzi spuštěním příkazu `az --version`. Informace o instalaci nebo upgradu verze Azure CLI najdete v tématu [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript
Aktualizujte skript pomocí ID vašeho předplatného.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění skriptu pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy uvedené v následující tabulce:

| **Příkaz** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [AZ MariaDB Server Create](/cli/azure/mariadb/server#az-mariadb-server-create) | Vytvoří server MariaDB, který je hostitelem databází. |
| [AZ MariaDB Server Update](/cli/azure/mariadb/server#az-mariadb-server-update) | Aktualizuje vlastnosti serveru MariaDB. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Vypíše hodnotu metriky pro prostředky. |
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace o [Azure Database for MariaDB COMPUTE a úložišti](../concepts-pricing-tiers.md)
- Vyzkoušejte další skripty: [ukázky v Azure CLI pro Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Další informace o [Azure CLI](/cli/azure)
