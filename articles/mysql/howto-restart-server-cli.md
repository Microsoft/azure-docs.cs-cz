---
title: Restartovat server – Azure CLI – Azure Database for MySQL
description: Tento článek popisuje, jak můžete restartovat Azure Database for MySQL server pomocí Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c1fd688fbfd892e3d0dfc3ebb1712dd931e0ed39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063501"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Restartujte Azure Database for MySQL server pomocí azure cli
Toto téma popisuje, jak můžete restartovat Azure Database for MySQL server. Pravděpodobně bude nutné restartovat server z důvodů údržby, což způsobí krátký výpadek, protože server provádí operaci.

Restartování serveru bude blokováno, pokud je služba zaneprázdněna. Služba může například zpracovávat dříve požadovanou operaci, jako je například škálování virtuálních jader.

Doba potřebná k dokončení restartování závisí na procesu obnovení MySQL. Chcete-li zkrátit dobu restartování, doporučujeme minimalizovat množství aktivity, ke kterým na serveru dochází před restartováním.

## <a name="prerequisites"></a>Požadavky
Chcete-li dokončit tento návod, potřebujete:
- [Databáze Azure pro server MySQL](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Tento návod vyžaduje, abyste používali Azure CLI verze 2.0 nebo novější. Chcete-li verzi potvrdit, zadejte `az --version`na příkazovém řádku příkazového řádku Azure . Informace o instalaci nebo upgradu najdete [v tématu Instalace příkazového příkazového příkazu k webu Azure]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Restartování serveru

Restartujte server pomocí následujícího příkazu:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Další kroky

Informace o [nastavení parametrů v Azure Database for MySQL](howto-configure-server-parameters-using-cli.md)