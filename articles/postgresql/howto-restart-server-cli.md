---
title: Restartovat server – Azure CLI – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje, jak můžete restartovat databázi Azure pro PostgreSQL – jeden server pomocí azure cli
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 13b26b545f9e95ce2457e4f8d9cf32da59cd91e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770148"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Restartujte Azure Database for PostgreSQL – jeden server pomocí azure cli
Toto téma popisuje, jak můžete restartovat Azure Database pro PostgreSQL server. Pravděpodobně bude nutné restartovat server z důvodů údržby, což způsobí krátký výpadek, protože server provádí operaci.

Restartování serveru bude blokováno, pokud je služba zaneprázdněna. Služba může například zpracovávat dříve požadovanou operaci, jako je například škálování virtuálních jader.
 
Doba potřebná k dokončení restartování závisí na procesu obnovení PostgreSQL. Chcete-li zkrátit dobu restartování, doporučujeme minimalizovat množství aktivity, ke kterým na serveru dochází před restartováním.

## <a name="prerequisites"></a>Požadavky
Chcete-li dokončit tento návod, potřebujete:
- [Databáze Azure pro postgreSQL server](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Tento návod vyžaduje, abyste používali Azure CLI verze 2.0 nebo novější. Chcete-li verzi potvrdit, zadejte `az --version`na příkazovém řádku příkazového řádku Azure . Informace o instalaci nebo upgradu najdete [v tématu Instalace příkazového příkazového příkazu k webu Azure]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Restartování serveru

Restartujte server pomocí následujícího příkazu:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Další kroky

Informace o [nastavení parametrů v Azure Database for PostgreSQL](howto-configure-server-parameters-using-cli.md)