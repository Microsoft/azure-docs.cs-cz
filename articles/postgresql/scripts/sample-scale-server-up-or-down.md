---
title: Skript příkazového příkazového příkazu Azure – škálování a monitorování databáze Azure pro PostgreSQL
description: Ukázkový skript Azure CLI – Škálování serveru Azure Database for PostgreSQL na jinou úroveň výkonu po dotazování metrik.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: 24aaf461576e6e043979660f9de968358763e003
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "68882985"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Monitorování a škálování jednoho serveru PostgreSQL pomocí Azure CLI
Tento ukázkový skript příkazového příkazového příkazu škáluje výpočetní prostředky a úložiště pro jeden azure database pro postgresql server po dotazování na metriky. Výpočetní prostředky mohou vertikálně navýšit nebo snížit kapacitu. Úložiště může pouze vertikálně navýšit kapacitu. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Zkontrolujte verzi spuštěním příkazu `az --version`. Informace o instalaci nebo upgradu verze Azure CLI najdete v tématu [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript
Aktualizujte skript pomocí ID předplatného.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění skriptu pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy uvedené v následující tabulce:

| **Příkaz** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az postgres server create](/cli/azure/postgres/server#az-postgres-server-create) | Vytvoří server PostgreSQL, který je hostitelem databází. |
| [aktualizace serveru az postgres](/cli/azure/postgres/server#az-postgres-server-update) | Aktualizuje vlastnosti serveru PostgreSQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Vypíše hodnotu metriky pro prostředky. |
| [az group delete](/cli/azure/group) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace o [výpočetních a úložných službě Azure Database for PostgreSQL](../concepts-pricing-tiers.md)
- Vyzkoušejte i další skripty: [Ukázky v Azure CLI pro službu Azure Database for PostgreSQL](../sample-scripts-azure-cli.md).
- Další informace o [azure cli](/cli/azure)
