---
title: Správa replik čtení – Azure CLI, REST API – Azure Database for PostgreSQL – jeden server
description: Zjistěte, jak spravovat repliky čtení v Azure Database for PostgreSQL – jeden server z rozhraní API Azure a rozhraní REST API
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b10ac3b4bc9dacd723b8b1265911df721b781189
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774810"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Vytváření a správa replik čtení z rozhraní API Azure, rozhraní REST API

V tomto článku se dozvíte, jak vytvořit a spravovat repliky čtení v Azure Database for PostgreSQL pomocí rozhraní API Azure a rozhraní REST API. Další informace o replikách pro čtení naleznete v [přehledu](concepts-read-replicas.md).

## <a name="azure-cli"></a>Azure CLI
Můžete vytvářet a spravovat repliky čtení pomocí azure CLI.

### <a name="prerequisites"></a>Požadavky

- [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Databáze Azure pro PostgreSQL server](quickstart-create-server-up-azure-cli.md) má být hlavní server.


### <a name="prepare-the-master-server"></a>Příprava hlavního serveru
Tyto kroky musí být použity k přípravě hlavního serveru v úrovních pro obecné účely nebo optimalizované pro paměť.

Parametr `azure.replication_support` musí být nastaven na **repliku** na hlavním serveru. Při změně tohoto statického parametru je nutné restartovat server, aby se změna projevila.

1. Nastavte `azure.replication_support` na REPLIKU.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

> [!NOTE]
> Pokud se při pokusu o nastavení azure.replication_support z rozhraní příkazového příkazu Azure zobrazí chyba "Neplatná hodnota je uvedena", je pravděpodobné, že váš server již má ve výchozím nastavení repliku. Chyba brání tomu, aby se toto nastavení správně projevilo na novějších serverech, kde replika je interní výchozí. <br><br>
> Můžete přeskočit kroky předlohy přípravy a přejít k vytvoření repliky. <br><br>
> Pokud chcete potvrdit, že váš server je v této kategorii, navštivte stránku replikace serveru na webu Azure Portal. "Zakázat replikaci" bude zobrazeno šedě a "Přidat repliku" bude aktivní v panelu nástrojů.

2. Chcete-li změnu použít, restartujte server.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

Příkaz [vytvořit repliku serveru az postgres](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Popis  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Skupina prostředků, kde bude vytvořen replika server.  |
| jméno | mydemoserver-replika | Název nového serveru repliky, který je vytvořen. |
| source-server | mydemoserver | ID názvu nebo prostředku existujícího hlavního serveru, ze kterýmá má být replikován. |

V příkladu CLI níže je replika vytvořena ve stejné oblasti jako předloha.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Chcete-li vytvořit repliku pro `--location` čtení mezi oblastmi, použijte parametr. Příklad cli níže vytvoří repliku v západní USA.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Další informace o oblastech, ve kterých můžete vytvořit repliku, naleznete v [článku koncepty replik pro čtení](concepts-read-replicas.md). 

Pokud jste nenastavili `azure.replication_support` parametr **replika** na serveru pro obecné účely nebo optimalizované pro paměť a restartovali server, zobrazí se chyba. Před vytvořením repliky proveďte tyto dva kroky.

Replika je vytvořena pomocí stejného nastavení výpočetních prostředků a úložiště jako předloha. Po vytvoření repliky lze změnit několik nastavení nezávisle na hlavním serveru: generování výpočetních prostředků, virtuální jádra, úložiště a záložní retenční období. Cenovou úroveň lze také změnit nezávisle, s výjimkou úrovně Basic nebo z ní.

> [!IMPORTANT]
> Před aktualizací nastavení hlavního serveru na novou hodnotu aktualizujte nastavení repliky na stejnou nebo vyšší hodnotu. Tato akce pomáhá replika držet krok s všechny změny provedené v předloze.

### <a name="list-replicas"></a>Seznam replik
Seznam replik hlavního serveru můžete zobrazit pomocí příkazu [az postgres server replica list.](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list)

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Zastavení replikace na replikační server
Replikace mezi hlavním serverem a replikou pro čtení můžete zastavit pomocí příkazu [stop repliky serveru az postgres.](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop)

Po zastavení replikace na hlavní server a repliku pro čtení ji nelze vrátit zpět. Replika pro čtení se stane samostatným serverem, který podporuje čtení i zápisy. Samostatný server nelze znovu vytvořit v replice.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Odstranění hlavního serveru nebo repliky serveru
Chcete-li odstranit hlavní nebo replikový server, použijte příkaz [odstranění serveru az postgres.](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete)

Při odstranění hlavního serveru je zastavena replikace všech replik čtení. Čtení repliky stát samostatné servery, které nyní podporují čtení i zápisy.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
Můžete vytvářet a spravovat repliky čtení pomocí [rozhraní Azure REST API](/rest/api/azure/).

### <a name="prepare-the-master-server"></a>Příprava hlavního serveru
Tyto kroky musí být použity k přípravě hlavního serveru v úrovních pro obecné účely nebo optimalizované pro paměť.

Parametr `azure.replication_support` musí být nastaven na **repliku** na hlavním serveru. Při změně tohoto statického parametru je nutné restartovat server, aby se změna projevila.

1. Nastavte `azure.replication_support` na REPLIKU.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [Chcete-li změnu](/rest/api/postgresql/servers/restart) použít, restartujte server.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení
Repliku pro čtení můžete vytvořit pomocí [rozhraní CREATE API](/rest/api/postgresql/servers/create):

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Další informace o oblastech, ve kterých můžete vytvořit repliku, naleznete v [článku koncepty replik pro čtení](concepts-read-replicas.md). 

Pokud jste nenastavili `azure.replication_support` parametr **replika** na serveru pro obecné účely nebo optimalizované pro paměť a restartovali server, zobrazí se chyba. Před vytvořením repliky proveďte tyto dva kroky.

Replika je vytvořena pomocí stejného nastavení výpočetních prostředků a úložiště jako předloha. Po vytvoření repliky lze změnit několik nastavení nezávisle na hlavním serveru: generování výpočetních prostředků, virtuální jádra, úložiště a záložní retenční období. Cenovou úroveň lze také změnit nezávisle, s výjimkou úrovně Basic nebo z ní.


> [!IMPORTANT]
> Před aktualizací nastavení hlavního serveru na novou hodnotu aktualizujte nastavení repliky na stejnou nebo vyšší hodnotu. Tato akce pomáhá replika držet krok s všechny změny provedené v předloze.

### <a name="list-replicas"></a>Seznam replik
Seznam replik hlavního serveru můžete zobrazit pomocí [rozhraní API seznamu replik](/rest/api/postgresql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Zastavení replikace na replikační server
Replikaci mezi hlavním serverem a replikou pro čtení můžete zastavit pomocí [rozhraní API pro aktualizaci](/rest/api/postgresql/servers/update).

Po zastavení replikace na hlavní server a repliku pro čtení ji nelze vrátit zpět. Replika pro čtení se stane samostatným serverem, který podporuje čtení i zápisy. Samostatný server nelze znovu vytvořit v replice.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Odstranění hlavního serveru nebo repliky serveru
Chcete-li odstranit hlavní nebo replikový server, použijte [rozhraní DELETE API](/rest/api/postgresql/servers/delete):

Při odstranění hlavního serveru je zastavena replikace všech replik čtení. Čtení repliky stát samostatné servery, které nyní podporují čtení i zápisy.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Další kroky
* Další informace o [čtení replik v Azure Database for PostgreSQL](concepts-read-replicas.md).
* Přečtěte si, jak [vytvářet a spravovat repliky pro čtení na webu Azure Portal](howto-read-replicas-portal.md).
