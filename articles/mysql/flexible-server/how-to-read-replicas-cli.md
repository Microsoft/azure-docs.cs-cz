---
title: Spravujte repliky čtení v Azure Database for MySQL flexibilním serveru pomocí Azure CLI.
description: Naučte se, jak nastavit a spravovat repliky pro čtení v Azure Database for MySQL flexibilním serveru pomocí Azure CLI.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/23/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a7d63fd76a88430495c9f55200308f63b11c89d4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494303"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Jak vytvářet a spravovat repliky pro čtení v Azure Database for MySQL flexibilním serveru pomocí Azure CLI

> [!IMPORTANT]
> Čtení replik v Azure Database for MySQL-flexibilní Server je ve verzi Preview.

V tomto článku se naučíte, jak vytvářet a spravovat repliky pro čtení v Azure Database for MySQL flexibilním serveru pomocí Azure CLI. Další informace o replikách pro čtení najdete v tématu [Přehled](concepts-read-replicas.md).

> [!Note]
> Replika není podporovaná na serveru s povolenou vysokou dostupností. 

## <a name="azure-cli"></a>Azure CLI
Repliky pro čtení můžete vytvořit a spravovat pomocí rozhraní příkazového řádku Azure CLI.

### <a name="prerequisites"></a>Předpoklady

- [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli)
- [Azure Database for MySQL flexibilní Server](quickstart-create-server-cli.md) , který se bude používat jako zdrojový server.

### <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

> [!IMPORTANT]
> Když vytvoříte repliku pro zdroj, který nemá žádné existující repliky, zdroj se nejdřív restartuje, aby se připravil pro replikaci. Vezměte v úvahu a udělejte tyto operace v době mimo špičku.

Server repliky pro čtení se dá vytvořit pomocí následujícího příkazu:

```azurecli-interactive
az mysql flexible-server replica create --replica-name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
``` 

> [!NOTE]
> Repliky čtení se vytvářejí se stejnou konfigurací serveru jako zdroj. Konfiguraci serveru repliky je možné po vytvoření změnit. Server repliky se vždycky vytvoří ve stejné skupině prostředků, stejné lokalitě a stejném předplatném jako zdrojový server. Pokud chcete vytvořit server repliky pro jinou skupinu prostředků nebo jiné předplatné, můžete [server repliky](../../azure-resource-manager/management/move-resource-group-and-subscription.md) po vytvoření přesunout. Doporučuje se udržovat konfiguraci serveru repliky ve stejné nebo větší hodnotě než zdroj, aby bylo zajištěno, že je replika schopná udržet se zdrojem.


### <a name="list-replicas-for-a-source-server"></a>Vypíše repliky pro zdrojový server.

Chcete-li zobrazit všechny repliky pro daný zdrojový server, spusťte následující příkaz: 

```azurecli-interactive
az mysql flexible-server replica list --server-name mydemoserver --resource-group myresourcegroup
```

### <a name="stop-replication-to-a-replica-server"></a>Zastavení replikace na server repliky

> [!IMPORTANT]
> Zastavení replikace na serveru je nevratné. Po zastavení replikace mezi zdrojem a replikou je nelze vrátit zpět. Server repliky se pak stal samostatným serverem a teď podporuje čtení i zápis. Tento server nelze znovu vytvořit do repliky.

Replikaci na server repliky pro čtení lze zastavit pomocí následujícího příkazu:

```azurecli-interactive
az mysql flexible-server replica stop-replication --replica-name mydemoreplicaserver --resource-group myresourcegroup
```

### <a name="delete-a-replica-server"></a>Odstranění serveru repliky

Odstranění serveru repliky pro čtení se dá udělat spuštěním příkazu **[AZ MySQL server Delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Odstranění zdrojového serveru

> [!IMPORTANT]
> Odstraněním zdrojového serveru se zastaví replikace na všechny servery replik a odstraní se samotný zdrojový server. Ze serverů replik se stanou samostatné servery, které teď podporují čtení i zápis.

Pokud chcete odstranit zdrojový server, můžete spustit příkaz **[AZ MySQL Flexible-Server Delete](/cli/azure/mysql/flexible-server)** .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Další kroky

- Další informace o [replikách pro čtení](concepts-read-replicas.md)