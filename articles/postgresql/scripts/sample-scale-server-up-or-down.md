---
title: "Skript Azure CLI – Škálování Azure Database for PostgreSQL"
description: "Ukázkový skript Azure CLI – Škálování serveru Azure Database for PostgreSQL na jinou úroveň výkonu po dotazování metrik."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.custom: mvc
ms.topic: sample
ms.date: 02/28/2018
ms.openlocfilehash: 21021485a7fe5f9bf32da76b507360290b8cfe66
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Monitorování a škálování jednoho serveru PostgreSQL pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku po dotazování metrik škáluje jeden server Azure Database for PostgreSQL na jinou úroveň výkonu. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Zkontrolujte verzi spuštěním příkazu `az --version`. Informace o instalaci nebo upgradu verze Azure CLI najdete v tématu [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript
V tomto ukázkovém skriptu upravte zvýrazněné řádky a aktualizujte uživatelské jméno a heslo správce na své vlastní. Nahraďte ID předplatného použité v příkazech `az monitor` vlastním ID předplatného.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=18-19 "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění skriptu pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy uvedené v následující tabulce:

| **Příkaz** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Vytvoří server PostgreSQL, který je hostitelem databází. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) | Vypíše hodnotu metriky pro prostředky. |
| [az group delete](/cli/azure/group#az_group_delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace o Azure CLI najdete tady: [Dokumentace k Azure CLI](/cli/azure/overview).
- Vyzkoušejte i další skripty: [Ukázky v Azure CLI pro službu Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- Další informace o škálování najdete tady: [Úrovně služby](../concepts-service-tiers.md) a [Výpočetní jednotky a jednotky úložiště](../concepts-compute-unit-and-storage.md).
