---
title: Příklad příkazového příkazového příkazu škáluje databázi SQL elastického fondu Azure SQL Database
description: Ukázkový skript Azure CLI pro škálování elastického fondu v Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: b229aa8976705c5e3ad83c468ebc10a261f14a4f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067392"
---
# <a name="use-cli-to-scale-an-elastic-pool-in-azure-sql-database"></a>Použití příkazového příkazového příkazu k škálování elastického fondu v Azure SQL Database

Tento příklad skriptu Azure CLI vytvoří elastické fondy, přesune sdružené databáze a změní výpočetní velikoste elastického fondu.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Spuštění skriptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Move database between pools")]

### <a name="clean-up-deployment"></a>Vyčištění nasazení

Pomocí následujícího příkazu odeberte skupinu prostředků a všechny k ní spojené prostředky.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Odkaz na vzorek

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| | |
|---|---|
| [az sql server](/cli/azure/sql/server) | Serverové příkazy. |
| [az SQL DB](/cli/azure/sql/db) | Příkazy databáze. |
| [az sql elastické fondy](/cli/azure/sql/elastic-pool) | Příkazy elastického fondu. |

## <a name="next-steps"></a>Další kroky

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../sql-database-cli-samples.md).
