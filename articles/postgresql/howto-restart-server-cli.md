---
title: Restart serveru – Azure CLI – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje, jak můžete restartovat server s Azure Database for PostgreSQL pomocí Azure CLI.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: af870c495ae707dd8319645eca979f0906234b4f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608370"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Restartování Azure Database for PostgreSQL – jeden server pomocí Azure CLI
Toto téma popisuje, jak můžete restartovat server Azure Database for PostgreSQL. Možná budete muset restartovat server z důvodů údržby, což způsobí krátký výpadek, protože server tuto operaci provede.

Pokud je služba zaneprázdněná, restart serveru se zablokuje. Například služba může zpracovávat dříve požadovanou operaci, jako je například škálování virtuální jádra.
 
> [!NOTE] 
> Čas potřebný k dokončení restartování závisí na procesu obnovení PostgreSQL. Chcete-li zkrátit dobu restartování, doporučujeme, abyste minimalizovali množství aktivity, ke kterým došlo na serveru před restartováním. Můžete taky chtít zvýšit frekvenci kontrolního bodu. Můžete také ladit hodnoty parametrů souvisejících s kontrolními body `max_wal_size` , včetně. Doporučuje se také spustit `CHECKPOINT` příkaz před restartováním serveru.

## <a name="prerequisites"></a>Požadavky
Postup dokončení této příručky:
- Vytvořte [server Azure Database for PostgreSQL](quickstart-create-server-up-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Tento článek vyžaduje Azure CLI verze 2,0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="restart-the-server"></a>Restartování serveru

Restartujte server pomocí následujícího příkazu:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Další kroky

Informace o [Nastavení parametrů v Azure Database for PostgreSQL](howto-configure-server-parameters-using-cli.md)
