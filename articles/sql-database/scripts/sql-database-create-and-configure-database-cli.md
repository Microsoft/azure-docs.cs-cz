---
title: Příklad rozhraní příkazového řádku – Vytvoření databáze SQL Azure | Microsoft Docs
description: Pomocí tohoto ukázkového skriptu Azure CLI vytvoříte databázi SQL.
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: janeng
editor: carlrab
ms.service: sql-database
ms.custom: DBs & servers, mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.author: carlrab
ms.date: 09/07/2018
ms.openlocfilehash: 8529791355e93a3a5001a5e41037ee6daeb31381
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44049707"
---
# <a name="use-cli-to-create-a-single-azure-sql-database-and-configure-a-firewall-rule"></a>Použití rozhraní příkazového řádku k vytvoření jedné databáze SQL Azure a konfiguraci pravidla brány firewall

Tento ukázkový skript Azure CLI vytvoří databázi SQL Azure a nakonfiguruje pravidlo brány firewall na úrovni serveru. Po úspěšném spuštění skriptu bude služba SQL Database přístupná ze všech služeb Azure a nakonfigurovaných IP adres. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh?highlight=9-10 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az sql server create](/cli/azure/sql/server#az_sql_server_create) | Vytvoří logický server, který je hostitelem služby SQL Database. |
| [az sql server firewall create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) | Vytvoří pravidlo brány firewall umožňující přístup ke všem databázím SQL na serveru ze zadaného rozsahu IP adres. |
| [az sql db create](/cli/azure/sql/db#az_sql_db_create) | Vytvoří na logickém serveru službu SQL Database. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../sql-database-cli-samples.md).

