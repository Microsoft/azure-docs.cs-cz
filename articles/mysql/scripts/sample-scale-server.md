---
title: Skript Azure CLI – Škálování serveru Azure Database for MySQL
description: Tento ukázkový skript rozhraní příkazového řádku po dotazování metrik škáluje server Azure Database for MySQL na jinou úroveň výkonu.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 08/01/2019
ms.openlocfilehash: 7a88686666d399d37229dd75897e0b926b655131
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728864"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Monitorování a škálování serveru Azure Database for MySQL pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku škáluje výpočetní výkon a úložiště pro jeden Azure Database for MySQL server po dotazování metrik.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Zkontrolujte verzi spuštěním příkazu `az --version`. Informace o instalaci nebo upgradu verze Azure CLI najdete v tématu [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript
V tomto ukázkovém skriptu upravte zvýrazněné řádky a aktualizujte uživatelské jméno a heslo správce na své vlastní. Nahraďte ID předplatného použité `az monitor` v příkazech vlastním ID předplatného.  
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh?highlight=15-16 "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění skriptu pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy uvedené v následující tabulce:

| **Příkaz** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Vytvoří server MySQL, který je hostitelem databází. |
| [AZ MySQL Server Update](/cli/azure/mysql/server#az-mysql-server-update) | Aktualizuje vlastnosti serveru MySQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Vypíše hodnotu metriky pro prostředky. |
| [az group delete](/cli/azure/group#az-group-delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace o [Azure Database for MySQL COMPUTE a úložišti](../concepts-pricing-tiers.md)
- Vyzkoušejte další skripty: [Ukázky v Azure CLI pro Azure Database for MySQL](../sample-scripts-azure-cli.md)
- Další informace o [Azure CLI](/cli/azure)