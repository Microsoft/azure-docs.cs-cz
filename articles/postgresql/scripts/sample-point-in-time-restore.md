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
ms.openlocfilehash: 4905006baf92161932cf1d2ce0660aaf52560c90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707621"
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>Obnovení serveru Azure Database for PostgreSQL pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku obnoví jeden server Azure Database for PostgreSQL k dřívějšímu bodu v čase.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Zkontrolujte verzi spuštěním příkazu `az --version`. Informace o instalaci nebo upgradu verze Azure CLI najdete v tématu [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript
V tomto ukázkovém skriptu upravte zvýrazněné řádky a aktualizujte uživatelské jméno a heslo správce na své vlastní. Nahraďte ID předplatného použité v příkazech `az monitor` vlastním ID předplatného.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=15-16 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění skriptu pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy uvedené v následující tabulce:

| **Příkaz** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az postgresql server create](/cli/azure/postgres/server#az-postgres-server-create) | Vytvoří server PostgreSQL, který je hostitelem databází. |
| [az postgresql server restore](/cli/azure/postgres/server#az-postgres-server-restore) | Obnoví server ze zálohy. |
| [az group delete](/cli/azure/group) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace najdete v dokumentaci Azure CLI: [dokumentace k Azure CLI](/cli/azure).
- Vyzkoušejte i další skripty: [Ukázky v Azure CLI pro službu Azure Database for PostgreSQL](../sample-scripts-azure-cli.md).
- [Zálohování a obnovení serveru ve službě Azure Database for PostgreSQL pomocí webu Azure Portal](../howto-restore-server-portal.md)
