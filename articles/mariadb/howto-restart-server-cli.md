---
title: Restartovat server – Azure CLI – databáze Azure pro MariaDB
description: Tento článek popisuje, jak můžete restartovat azure databáze pro mariadb server pomocí azure cli.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: f5572611b99245fd62b4e0a9d73e6ed728e42f5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530644"
---
# <a name="restart-azure-database-for-mariadb-server-using-the-azure-cli"></a>Restartujte azure database pro server MariaDB pomocí příkazového příkazového příkazu k Azure
Toto téma popisuje, jak můžete restartovat azure databáze pro mariadb server. Pravděpodobně bude nutné restartovat server z důvodů údržby, což způsobí krátký výpadek, protože server provádí operaci.

Restartování serveru bude blokováno, pokud je služba zaneprázdněna. Služba může například zpracovávat dříve požadovanou operaci, jako je například škálování virtuálních jader.

Doba potřebná k dokončení restartování závisí na procesu obnovení MariaDB. Chcete-li zkrátit dobu restartování, doporučujeme minimalizovat množství aktivity, ke kterým na serveru dochází před restartováním.

## <a name="prerequisites"></a>Požadavky
Chcete-li dokončit tento návod, potřebujete:
- [Databáze Azure pro server MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Tento návod vyžaduje, abyste používali Azure CLI verze 2.0 nebo novější. Chcete-li verzi potvrdit, zadejte `az --version`na příkazovém řádku příkazového řádku Azure . Informace o instalaci nebo upgradu najdete [v tématu Instalace příkazového příkazového příkazu k webu Azure]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Restartování serveru

Restartujte server pomocí následujícího příkazu:

```azurecli-interactive
az mariadb server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Další kroky

Informace o [tom, jak nastavit parametry v Azure Database pro MariaDB](howto-configure-server-parameters-cli.md)