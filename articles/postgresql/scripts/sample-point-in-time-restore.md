---
title: Skript Azure CLI – Obnovení serveru Azure Database for PostgreSQL
description: Tento ukázkový skript Azure CLI ukazuje, jak obnovit server Azure Database for PostgreSQL a jeho databáze k dřívějšímu bodu v čase.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 6d95c06efa347618a7c571b65f01f147aeb5ef1f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778410"
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>Obnovení serveru Azure Database for PostgreSQL pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku obnoví jeden server Azure Database for PostgreSQL k dřívějšímu bodu v čase.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="sample-script"></a>Ukázkový skript
V tomto ukázkovém skriptu upravte zvýrazněné řádky a aktualizujte uživatelské jméno a heslo správce na své vlastní. Nahraďte ID předplatného použité v příkazech `az monitor` vlastním ID předplatného.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=15-16 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění skriptu pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy uvedené v následující tabulce:

| **Systému** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az postgresql server create](/cli/azure/postgres/server#az_postgres_server_create) | Vytvoří server PostgreSQL, který je hostitelem databází. |
| [az postgresql server restore](/cli/azure/postgres/server#az_postgres_server_restore) | Obnoví server ze zálohy. |
| [az group delete](/cli/azure/group) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace najdete v dokumentaci Azure CLI: [dokumentace k Azure CLI](/cli/azure).
- Vyzkoušejte i další skripty: [Ukázky v Azure CLI pro službu Azure Database for PostgreSQL](../sample-scripts-azure-cli.md).
- [Zálohování a obnovení serveru ve službě Azure Database for PostgreSQL pomocí webu Azure Portal](../howto-restore-server-portal.md)
