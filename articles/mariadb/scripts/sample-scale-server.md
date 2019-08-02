---
title: Skript Azure CLI – škálování serveru Azure Database for MariaDB
description: Tento ukázkový skript rozhraní příkazového řádku po dotazování metrik škáluje Azure Database for MariaDB Server na jinou úroveň výkonu.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 08/01/2019
ms.openlocfilehash: 99f85abe0c8f08bedb3e9808d2b740d4b7842257
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720364"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Monitorování a škálování serveru Azure Database for MariaDB pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku škáluje výpočetní výkon a úložiště pro jeden Azure Database for MariaDB Server po dotazování metrik.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Zkontrolujte verzi spuštěním příkazu `az --version`. Informace o instalaci nebo upgradu verze Azure CLI najdete v tématu [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript
V tomto ukázkovém skriptu upravte zvýrazněné řádky a aktualizujte uživatelské jméno a heslo správce na své vlastní. Nahraďte ID předplatného použité `az monitor` v příkazech vlastním ID předplatného.  
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh?highlight=15-16 "Create and scale Azure Database for MariaDB.")]

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

## <a name="next-steps"></a>Další postup
- Další informace o [Azure Database for MariaDB COMPUTE a úložišti](../concepts-pricing-tiers.md)
- Vyzkoušejte další skripty: [Ukázky v Azure CLI pro Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Další informace o [Azure CLI](/cli/azure)
