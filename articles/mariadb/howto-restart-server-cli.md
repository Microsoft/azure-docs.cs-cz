---
title: Restartování – Azure Database pro MariaDB serveru pomocí rozhraní příkazového řádku Azure
description: Tento článek popisuje, jak je možné restartovat serveru Azure Database for MariaDB pomocí Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/28/2019
ms.openlocfilehash: a6e0509d941d9bfdfe6db7a8b93ee49c5bece1a6
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "66171437"
---
# <a name="restart-azure-database-for-mariadb-server-using-the-azure-cli"></a>Restartování – Azure Database pro MariaDB serveru pomocí rozhraní příkazového řádku Azure
Toto téma popisuje, jak můžete Azure Database pro MariaDB server restartovat. Budete muset restartovat server z důvodů údržby, což způsobuje krátké výpadky, jak server provádí operaci.

Restartování serveru se zablokuje, pokud služba je zaneprázdněna. Například služba může zpracovávat dříve požadovaná operace, jako je například škálování virtuálních jader.

Čas potřebný k dokončení restartování závisí na MariaDB proces obnovení. Pokud chcete snížit čas restartování, doporučujeme, abyste že minimalizovali množství aktivit, k nimž došlo na serveru ještě před jejich restartování.

## <a name="prerequisites"></a>Požadavky
K dokončení této příručce s postupy, potřebujete:
- [– Azure Database pro MariaDB serveru](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Tato příručka vyžaduje použití Azure CLI verze 2.0 nebo novější. K potvrzení verze příkazového řádku Azure CLI, zadejte `az --version`. Pro instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Restartujte server

Restartujte server pomocí následujícího příkazu:

```azurecli-interactive
az mariadb server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Další postup

Další informace o [jak nastavit parametry ve službě Azure Database pro MariaDB](howto-configure-server-parameters-cli.md)