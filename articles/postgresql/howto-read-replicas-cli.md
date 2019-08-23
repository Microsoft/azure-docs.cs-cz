---
title: Správa replik pro čtení pro jeden server Azure Database for PostgreSQL z Azure CLI
description: Naučte se spravovat repliky pro čtení v Azure Database for PostgreSQL jednom serveru z Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 63a8acad3c393a4c4d9c6a3b6750f1f934dad43d
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907429"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Vytváření a Správa replik pro čtení z Azure CLI

V tomto článku se dozvíte, jak vytvářet a spravovat repliky pro čtení v Azure Database for PostgreSQL z rozhraní příkazového řádku Azure CLI. Další informace o replikách pro čtení najdete v tématu [Přehled](concepts-read-replicas.md).

> [!IMPORTANT]
> Repliku pro čtení můžete vytvořit ve stejné oblasti jako váš hlavní server nebo v libovolné jiné oblasti Azure podle vašeho výběru. Replikace mezi oblastmi je aktuálně ve verzi Public Preview.

## <a name="prerequisites"></a>Požadavky
- [Server Azure Database for PostgreSQL](quickstart-create-server-up-azure-cli.md) , který bude hlavním serverem.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 


## <a name="prepare-the-master-server"></a>Příprava hlavního serveru
Tyto kroky je nutné použít k přípravě hlavního serveru v Pro obecné účely nebo paměťově optimalizovaných úrovních.

Parametr musí být nastaven na repliku na hlavním serveru. `azure.replication_support` Pokud se tento statický parametr změní, je nutné restartovat server, aby se změna projevila.

1. Nastavte `azure.replication_support` na repliku.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Restartujte, aby se změny projevily na serveru.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

Příkaz [AZ Postgres Server Replica Create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Popis  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Skupina prostředků, ve které se vytvoří server repliky.  |
| name | mydemoserver-replica | Název nového serveru repliky, který je vytvořen. |
| source-server | mydemoserver | Název nebo ID prostředku existujícího hlavního serveru, ze kterého se má replikovat. |

V následujícím příkladu rozhraní příkazového řádku je replika vytvořená ve stejné oblasti jako hlavní.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Chcete-li vytvořit repliku čtení ve více oblastech `--location` , použijte parametr. Níže uvedený příklad rozhraní příkazového řádku vytvoří repliku v Západní USA.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Další informace o tom, které oblasti můžete vytvořit repliku v, najdete v [článku věnovaném konceptům pro čtení replik](concepts-read-replicas.md). 

Pokud jste `azure.replication_support` nenastavili parametr na repliku na pro obecné účely nebo paměťově optimalizovaném hlavním serveru a server restartovali, zobrazí se chyba. Před vytvořením repliky tyto dva kroky proveďte.

Replika se vytvoří pomocí stejného nastavení výpočtů a úložiště jako hlavní. Po vytvoření repliky se dá několik nastavení měnit nezávisle na hlavním serveru: generování výpočetních prostředků, virtuální jádra, úložiště a doba uchovávání záloh. Cenová úroveň se dá změnit také nezávisle, s výjimkou nebo z úrovně Basic.

> [!IMPORTANT]
> Než bude nastavení hlavního serveru aktualizováno na novou hodnotu, aktualizujte nastavení repliky na hodnotu rovná se nebo větší. Tato akce pomůže replice uchovávat všechny změny provedené v hlavní větvi.

## <a name="list-replicas"></a>Vypsat repliky
Seznam replik hlavního serveru můžete zobrazit pomocí příkazu [AZ Postgres Server Replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) .

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>Zastavuje se replikace na serveru repliky
Replikaci mezi hlavním serverem a replikou pro čtení můžete zastavit pomocí příkazu [AZ Postgres Server Replica stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) .

Po zastavení replikace na hlavní server a repliku pro čtení ji nejde vrátit zpět. Replika čtení se stal samostatným serverem, který podporuje čtení i zápis. Samostatný server se nedá znovu vytvořit do repliky.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>Odstranění hlavního serveru nebo serveru repliky
Pokud chcete odstranit hlavní server nebo server repliky, použijte příkaz [AZ Postgres Server Delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) .

Při odstranění hlavního serveru se zastaví replikace do všech replik čtení. Repliky čtení se stanou samostatnými servery, které nyní podporují čtení i zápis.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Další postup
* Přečtěte si další informace o [replikách pro čtení v Azure Database for PostgreSQL](concepts-read-replicas.md).
* Naučte se [vytvářet a spravovat repliky pro čtení v Azure Portal](howto-read-replicas-portal.md).