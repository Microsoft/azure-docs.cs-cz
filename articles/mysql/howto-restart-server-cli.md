---
title: Restartovat – Azure Database for MySQL pomocí Azure CLI
description: Tento článek popisuje, jak je možné restartovat Azure Database for MySQL pomocí Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/28/2019
ms.openlocfilehash: d00aa35437f93c010ce48f3036b4a684910702c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160433"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>Restartovat – Azure Database for MySQL pomocí Azure CLI
Toto téma popisuje, jak je možné restartovat serveru Azure Database for MySQL. Budete muset restartovat server z důvodů údržby, což způsobuje krátké výpadky, jak server provádí operaci.

Restartování serveru se zablokuje, pokud služba je zaneprázdněna. Například služba může zpracovávat dříve požadovaná operace, jako je například škálování virtuálních jader.

Čas potřebný k dokončení restartování závisí na proces obnovení MySQL. Pokud chcete snížit čas restartování, doporučujeme, abyste že minimalizovali množství aktivit, k nimž došlo na serveru ještě před jejich restartování.

## <a name="prerequisites"></a>Požadavky
K dokončení této příručce s postupy, potřebujete:
- [– Azure Database for MySQL serveru](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Tato příručka vyžaduje použití Azure CLI verze 2.0 nebo novější. K potvrzení verze příkazového řádku Azure CLI, zadejte `az --version`. Pro instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Restartujte server

Restartujte server pomocí následujícího příkazu:

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Další postup

Další informace o [jak nastavit parametry ve službě Azure Database for MySQL](howto-configure-server-parameters-using-cli.md)