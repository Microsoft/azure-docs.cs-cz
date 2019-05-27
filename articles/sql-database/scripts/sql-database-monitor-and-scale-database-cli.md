---
title: Příklad rozhraní příkazového řádku – Monitorování a škálování izolované databáze Azure SQL | Microsoft Docs
description: Ukázkový skript Azure CLI pro monitorování a škálování izolované databáze Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: eeb932123e57dcb10fce3baf9670d8c1ae961fb8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66151668"
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>Monitorování a škálování izolované databáze SQL pomocí rozhraní příkazového řádku

Tento ukázkový skript Azure CLI škáluje izolovanou databázi Azure SQL na jinou velikost výpočetních prostředků poté, co se odeslal dotaz na informace o velikosti databáze.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

> [!TIP]
> Pomocí příkazu [az sql db op list](/cli/azure/sql/db/op?#az-sql-db-op-list) získáte seznam operací provedených s databází a pomocí příkazu [az sql db op cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) zrušíte operaci aktualizace databáze.

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Použijte následující příkaz k odebrání skupiny prostředků a všechny prostředky, které s ním spojená.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az-sql-server-create) | Vytvoří server služby SQL Database, který je hostitelem izolovaných databází a elastických fondů. |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql#az-sql-show-usage) | Zobrazuje informace o využití velikosti databáze jedné nebo ve fondu. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) | Aktualizuje vlastnosti databáze (například úroveň služby nebo velikost výpočetních prostředků) nebo přesune databázi do nebo z elastického fondu nebo mezi elastickými fondy. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další postup

Další informace o Azure CLI najdete v [dokumentaci k Azure CLI](https://docs.microsoft.com/cli/azure).

Další ukázkové skripty rozhraní příkazového řádku pro službu SQL Database najdete v [dokumentaci ke službě Azure SQL Database](../sql-database-cli-samples.md).
