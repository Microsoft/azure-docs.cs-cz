---
title: Správa replik čtení – Azure CLI, REST API-Azure Database for MySQL
description: Naučte se, jak nastavit a spravovat repliky pro čtení v Azure Database for MySQL pomocí rozhraní příkazového řádku Azure CLI nebo REST API.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1a5bc9638e2e6eeff8f2176247f579b64beede90
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94540208"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>Vytvoření a Správa replik pro čtení v Azure Database for MySQL pomocí rozhraní příkazového řádku Azure a REST API

V tomto článku se naučíte, jak vytvářet a spravovat repliky pro čtení ve službě Azure Database for MySQL pomocí rozhraní příkazového řádku Azure a REST API. Další informace o replikách pro čtení najdete v tématu [Přehled](concepts-read-replicas.md).

## <a name="azure-cli"></a>Azure CLI
Repliky pro čtení můžete vytvořit a spravovat pomocí rozhraní příkazového řádku Azure CLI.

### <a name="prerequisites"></a>Předpoklady

- [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli)
- [Server Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) , který se bude používat jako zdrojový server. 

> [!IMPORTANT]
> Funkce replika čtení je k dispozici pouze pro Azure Database for MySQL servery v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že je zdrojový server v jedné z těchto cenových úrovní.

### <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

> [!IMPORTANT]
> Když vytvoříte repliku pro zdroj, který nemá žádné existující repliky, zdroj se nejdřív restartuje, aby se připravil pro replikaci. Vezměte v úvahu a udělejte tyto operace v době mimo špičku.

Server repliky pro čtení se dá vytvořit pomocí následujícího příkazu:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mysql server replica create`Příkaz vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Description  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, do které se vytvoří server repliky.  |
| name | mydemoreplicaserver | Název nového serveru repliky, který se vytvoří. |
| source-server | mydemoserver | Název nebo ID existujícího zdrojového serveru, ze kterého se má replikovat. |

Chcete-li vytvořit repliku čtení ve více oblastech, použijte `--location` parametr. Níže uvedený příklad rozhraní příkazového řádku vytvoří repliku v Západní USA.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Další informace o tom, které oblasti můžete vytvořit repliku v, najdete v [článku věnovaném konceptům pro čtení replik](concepts-read-replicas.md). 

> [!NOTE]
> Repliky čtení se vytvářejí se stejnou konfigurací serveru jako hlavní. Konfiguraci serveru repliky je možné po vytvoření změnit. Doporučuje se udržovat konfiguraci serveru repliky ve stejné nebo větší hodnotě než zdroj, aby bylo zajištěno, že je replika schopná s hlavní hodnotou.


### <a name="list-replicas-for-a-source-server"></a>Vypíše repliky pro zdrojový server.

Chcete-li zobrazit všechny repliky pro daný zdrojový server, spusťte následující příkaz: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mysql server replica list`Příkaz vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Description  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, do které se vytvoří server repliky.  |
| název-serveru | mydemoserver | Název nebo ID zdrojového serveru. |

### <a name="stop-replication-to-a-replica-server"></a>Zastavení replikace na server repliky

> [!IMPORTANT]
> Zastavení replikace na serveru je nevratné. Po zastavení replikace mezi zdrojem a replikou je nelze vrátit zpět. Server repliky se pak stal samostatným serverem a teď podporuje čtení i zápis. Tento server nelze znovu vytvořit do repliky.

Replikaci na server repliky pro čtení lze zastavit pomocí následujícího příkazu:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

`az mysql server replica stop`Příkaz vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Description  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, ve které existuje server repliky.  |
| name | mydemoreplicaserver | Název serveru repliky, na kterém má být replikace zastavena. |

### <a name="delete-a-replica-server"></a>Odstranění serveru repliky

Odstranění serveru repliky pro čtení se dá udělat spuštěním příkazu **[AZ MySQL server Delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Odstranění zdrojového serveru

> [!IMPORTANT]
> Odstraněním zdrojového serveru se zastaví replikace na všechny servery replik a odstraní se samotný zdrojový server. Ze serverů replik se stanou samostatné servery, které teď podporují čtení i zápis.

Pokud chcete odstranit zdrojový server, můžete spustit příkaz **[AZ MySQL server Delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>REST API
Repliky pro čtení můžete vytvářet a spravovat pomocí [REST API Azure](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení
Repliku pro čtení můžete vytvořit pomocí [rozhraní API pro vytvoření](/rest/api/mysql/servers/create):

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Další informace o tom, které oblasti můžete vytvořit repliku v, najdete v [článku věnovaném konceptům pro čtení replik](concepts-read-replicas.md). 

Pokud jste nenastavili `azure.replication_support` parametr na **repliku** na pro obecné účely nebo paměťově optimalizovaném zdrojovém serveru a restartovali jste server, zobrazí se chyba. Před vytvořením repliky tyto dva kroky proveďte.

Replika se vytvoří pomocí stejného nastavení výpočtů a úložiště jako hlavní. Po vytvoření repliky se dá změnit několik nastavení nezávisle na zdrojovém serveru: generování výpočetních prostředků, virtuální jádra, úložiště a doba uchovávání záloh. Cenová úroveň se dá změnit také nezávisle, s výjimkou nebo z úrovně Basic.


> [!IMPORTANT]
> Než bude nastavení zdrojového serveru aktualizováno na novou hodnotu, aktualizujte nastavení repliky na hodnotu rovná se nebo větší. Tato akce pomůže replice uchovávat všechny změny provedené v hlavní větvi.

### <a name="list-replicas"></a>Vypsat repliky
Seznam replik zdrojového serveru můžete zobrazit pomocí [rozhraní API pro seznam replik](/rest/api/mysql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Zastavení replikace na server repliky
Replikaci mezi zdrojovým serverem a replikou pro čtení můžete zastavit pomocí [rozhraní API pro aktualizaci](/rest/api/mysql/servers/update).

Až zastavíte replikaci na zdrojový server a repliku pro čtení, nepůjde to vrátit zpátky. Replika čtení se stal samostatným serverem, který podporuje čtení i zápis. Samostatný server se nedá znovu vytvořit do repliky.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-source-or-replica-server"></a>Odstranění zdrojového serveru nebo serveru repliky
Pokud chcete odstranit zdrojový server nebo server repliky, použijte [rozhraní API pro odstranění](/rest/api/mysql/servers/delete):

Při odstranění zdrojového serveru se zastaví replikace do všech replik čtení. Repliky čtení se stanou samostatnými servery, které nyní podporují čtení i zápis.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Další kroky

- Další informace o [replikách pro čtení](concepts-read-replicas.md)