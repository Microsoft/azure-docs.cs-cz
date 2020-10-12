---
title: Restart serveru – Azure CLI – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje, jak můžete restartovat server s Azure Database for PostgreSQL pomocí Azure CLI.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 30770dd7ed71f4cde68c1a616a256bf60b5acaef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704714"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Restartování Azure Database for PostgreSQL – jeden server pomocí Azure CLI
Toto téma popisuje, jak můžete restartovat server Azure Database for PostgreSQL. Možná budete muset restartovat server z důvodů údržby, což způsobí krátký výpadek, protože server tuto operaci provede.

Pokud je služba zaneprázdněná, restart serveru se zablokuje. Například služba může zpracovávat dříve požadovanou operaci, jako je například škálování virtuální jádra.
 
Čas potřebný k dokončení restartování závisí na procesu obnovení PostgreSQL. Chcete-li zkrátit dobu restartování, doporučujeme, abyste minimalizovali množství aktivity, ke kterým došlo na serveru před restartováním.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto průvodce budete potřebovat:
- [Server Azure Database for PostgreSQL](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Tento návod vyžaduje použití Azure CLI verze 2,0 nebo novější. Verzi ověříte tak, že v příkazovém řádku Azure CLI zadáte `az --version` . Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Restartování serveru

Restartujte server pomocí následujícího příkazu:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Další kroky

Informace o [Nastavení parametrů v Azure Database for PostgreSQL](howto-configure-server-parameters-using-cli.md)
