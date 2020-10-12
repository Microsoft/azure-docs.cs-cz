---
title: Restart serveru – Azure CLI – Azure Database for MariaDB
description: Tento článek popisuje, jak můžete restartovat server Azure Database for MariaDB pomocí rozhraní příkazového řádku Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: c1e7db129b29cfa1109b3c79bd8590a99779e361
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87503085"
---
# <a name="restart-azure-database-for-mariadb-server-using-the-azure-cli"></a>Restartování serveru Azure Database for MariaDB pomocí rozhraní příkazového řádku Azure
Toto téma popisuje, jak můžete restartovat server Azure Database for MariaDB. Možná budete muset restartovat server z důvodů údržby, což způsobí krátký výpadek, protože server tuto operaci provede.

Pokud je služba zaneprázdněná, restart serveru se zablokuje. Například služba může zpracovávat dříve požadovanou operaci, jako je například škálování virtuální jádra.

Čas potřebný k dokončení restartování závisí na procesu obnovení MariaDB. Chcete-li zkrátit dobu restartování, doporučujeme, abyste minimalizovali množství aktivity, ke kterým došlo na serveru před restartováním.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto průvodce budete potřebovat:
- [Server Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Tento návod vyžaduje použití Azure CLI verze 2,0 nebo novější. Verzi ověříte tak, že v příkazovém řádku Azure CLI zadáte `az --version` . Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Restartování serveru

Restartujte server pomocí následujícího příkazu:

```azurecli-interactive
az mariadb server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Další kroky

Informace o [Nastavení parametrů v Azure Database for MariaDB](howto-configure-server-parameters-cli.md)
