---
title: 'Rozhraní příkazového řádku Azure CLI: vytvoření samostatné databáze'
description: Pomocí tohoto ukázkového skriptu Azure CLI vytvořte jedinou databázi.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: cb8850adaf638aca3ed69c3939916e132f66062d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86504559"
---
# <a name="use-the-azure-cli-to-create-a-single-database-and-configure-a-firewall-rule"></a>Použití rozhraní příkazového řádku Azure k vytvoření jedné databáze a konfiguraci pravidla brány firewall

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Tento ukázkový skript Azure CLI vytvoří v Azure SQL Database jedinou databázi a nakonfiguruje pravidlo brány firewall na úrovni serveru. Po úspěšném spuštění skriptu se k databázi dostanete ze všech služeb Azure a nakonfigurované IP adresy.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Spuštění skriptu

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Vzorový odkaz

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Popis |
|---|---|
| [az sql server](/cli/azure/sql/server#az-sql-server-create) | Příkazy serveru |
| [AZ SQL Server Firewall](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | Příkazy brány firewall serveru. |
| [AZ SQL DB](/cli/azure/sql/db#az-sql-db-create) | Příkazy databáze. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../az-cli-script-samples-content-guide.md).
