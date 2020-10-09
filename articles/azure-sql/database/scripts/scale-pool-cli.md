---
title: 'Azure CLI: škálování elastického fondu'
description: K horizontálnímu škálování elastického fondu v Azure SQL Database použijte ukázkový skript Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 01ccf7117cd17118941a00cc0480216bc436bc40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86514760"
---
# <a name="use-the-azure-cli-to-scale-an-elastic-pool-in-azure-sql-database"></a>Použití rozhraní příkazového řádku Azure k horizontálnímu navýšení kapacity elastického fondu v Azure SQL Database

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Tento ukázkový skript Azure CLI vytvoří elastické fondy v Azure SQL Database, přesune databáze ve fondu a změní výpočetní velikosti elastického fondu.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure CLI místně, musíte mít Azure CLI verze 2,0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Spuštění skriptu

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Move database between pools")]

### <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní přidružené prostředky.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Vzorový odkaz

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Popis |
|---|---|
| [az sql server](/cli/azure/sql/server) | Příkazy serveru. |
| [AZ SQL DB](/cli/azure/sql/db) | Příkazy databáze. |
| [AZ SQL elastické fondy](/cli/azure/sql/elastic-pool) | Příkazy elastického fondu. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../az-cli-script-samples-content-guide.md).
