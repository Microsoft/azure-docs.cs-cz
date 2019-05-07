---
title: Restartovat – Azure Database for PostgreSQL – jeden Server pomocí rozhraní příkazového řádku Azure
description: Tento článek popisuje, jak je možné restartovat službu Azure Database for PostgreSQL – jeden Server pomocí rozhraní příkazového řádku Azure
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 0a7cd815724fcebd6311860576e620eb9273523b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65068978"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Restartovat – Azure Database for PostgreSQL – jeden Server pomocí rozhraní příkazového řádku Azure
Toto téma popisuje, jak je možné restartovat serveru Azure Database for PostgreSQL. Budete muset restartovat server z důvodů údržby, což způsobuje krátké výpadky, jak server provádí operaci.

Restartování serveru se zablokuje, pokud služba je zaneprázdněna. Například služba může zpracovávat dříve požadovaná operace, jako je například škálování virtuálních jader.
 
Čas potřebný k dokončení restartování závisí na proces obnovení PostgreSQL. Pokud chcete snížit čas restartování, doporučujeme, abyste že minimalizovali množství aktivit, k nimž došlo na serveru ještě před jejich restartování.

## <a name="prerequisites"></a>Požadavky
K dokončení této příručce s postupy, potřebujete:
- [– Azure Database for postgresql – server](quickstart-create-server-up-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Tato příručka vyžaduje použití Azure CLI verze 2.0 nebo novější. K potvrzení verze příkazového řádku Azure CLI, zadejte `az --version`. Pro instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI]( /cli/azure/install-azure-cli).


## <a name="restart-the-server"></a>Restartujte server

Restartujte server pomocí následujícího příkazu:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Další postup

Další informace o [jak nastavit parametry ve službě Azure Database for PostgreSQL](howto-configure-server-parameters-using-cli.md)