---
title: Skript Azure CLI – Stažení protokolů serveru ve službě Azure Database for PostgreSQL
description: Tento ukázkový skript Azure CLI ukazuje, jak povolit a stáhnout protokoly serveru Azure Database for PostgreSQL.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 6042b58dc4187898985abad29d8050ed2be58a13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710307"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Povolení a stažení protokolů pomalých dotazů serveru Azure Database for PostgreSQL pomocí Azure CLI
Tento ukázkový skript rozhraní příkazového řádku povolí a stáhne protokoly pomalých dotazů jednoho serveru Azure Database for PostgreSQL.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít verzi Azure CLI 2.0 nebo novější. Zkontrolujte verzi spuštěním příkazu `az --version`. Informace o instalaci nebo upgradu verze Azure CLI najdete v tématu [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript
V tomto ukázkovém skriptu upravte zvýrazněné řádky a aktualizujte uživatelské jméno a heslo správce na své vlastní. &lt; &gt; V příkazech nahraďte Log_file_name `az monitor` vlastním názvem souboru protokolu serveru.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení
Po spuštění skriptu pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Vysvětlení skriptu
Tento skript používá příkazy uvedené v následující tabulce:

| **Příkaz** | **Poznámky** |
|---|---|
| [az group create](/cli/azure/group) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az postgres server create](/cli/azure/postgres/server) | Vytvoří server PostgreSQL, který je hostitelem databází. |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | Vypíše hodnoty konfigurace serveru. |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | Aktualizuje konfiguraci serveru. |
| [az postgres server-logs list](/cli/azure/postgres/server-logs) | Vypíše soubory protokolů serveru. |
| [az postgres server-logs download](/cli/azure/postgres/server-logs) | Stáhne soubory protokolů. |
| [az group delete](/cli/azure/group) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |

## <a name="next-steps"></a>Další kroky
- Další informace najdete v dokumentaci Azure CLI: [dokumentace k Azure CLI](/cli/azure).
- Vyzkoušejte i další skripty: [Ukázky v Azure CLI pro službu Azure Database for PostgreSQL](../sample-scripts-azure-cli.md).
- [Konfigurace protokolů serveru a přístup k nim na webu Azure Portal](../howto-configure-server-logs-in-portal.md)
