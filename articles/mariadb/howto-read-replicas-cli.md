---
title: Vytváření a Správa replik pro čtení v Azure Database for MariaDB – AZURE CLI REST API
description: Tento článek popisuje, jak nastavit a spravovat repliky pro čtení v Azure Database for MariaDB pomocí rozhraní příkazového řádku Azure a REST API.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 2f5029ccbf80551721ecc363aa4c3930961d9154
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993076"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Vytvoření a Správa replik pro čtení v Azure Database for MariaDB pomocí rozhraní příkazového řádku Azure a REST API

V tomto článku se naučíte, jak vytvářet a spravovat repliky pro čtení ve službě Azure Database for MariaDB pomocí rozhraní příkazového řádku Azure a REST API.

## <a name="azure-cli"></a>Azure CLI
Repliky pro čtení můžete vytvořit a spravovat pomocí rozhraní příkazového řádku Azure CLI.

### <a name="prerequisites"></a>Požadavky

- [Nainstalujte Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- [Server Azure Database for MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) , který se bude používat jako hlavní server. 

> [!IMPORTANT]
> Funkce replika čtení je k dispozici pouze pro Azure Database for MariaDB servery v cenové úrovni optimalizované pro Pro obecné účely nebo paměť. Ujistěte se, že se že hlavní server je v jednom z těchto cenové úrovně.

### <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

Server repliky pro čtení můžete vytvořit pomocí následujícího příkazu:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mariadb server replica create` Příkaz vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, ve kterém server repliky bude vytvořena.  |
| jméno | mydemoreplicaserver | Název nového serveru repliky, který je vytvořen. |
| source-server | mydemoserver | Název nebo ID existující hlavní server pro replikaci z. |

Chcete-li vytvořit repliku čtení ve více oblastech `--location` , použijte parametr. Níže uvedený příklad rozhraní příkazového řádku vytvoří repliku v Západní USA.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Další informace o tom, které oblasti můžete vytvořit repliku v, najdete v [článku věnovaném konceptům pro čtení replik](concepts-read-replicas.md). 

> [!NOTE]
> Repliky pro čtení jsou vytvořeny se stejnou konfigurací serveru na hlavní server. Konfigurace serveru repliky můžete po jejím vytvoření změnit. Doporučuje se, že konfigurace serveru repliky by udržováno na hodnoty roven nebo větší než hlavní Ujistěte se, že je replika schopné udržovat tempo s hlavní.

### <a name="list-replicas-for-a-master-server"></a>Seznam replik pro hlavní server

Chcete-li zobrazit všechny repliky pro danou hlavní server, spusťte následující příkaz: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mariadb server replica list` Příkaz vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, ve kterém server repliky bude vytvořena.  |
| název-serveru | mydemoserver | Název nebo ID hlavního serveru. |

### <a name="stop-replication-to-a-replica-server"></a>Zastavuje se replikace na serveru repliky

> [!IMPORTANT]
> Zastavuje se replikace na server je nevratná operace. Jakmile se zastaví replikace mezi hlavní a repliky, nejde vrátit. Server repliky pak stane samostatným serverem a nyní podporuje čtení a zápisu. Tento server nelze je převést na repliku znovu.

Replikace na server repliky pro čtení lze zastavit pomocí následujícího příkazu:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

`az mariadb server replica stop` Příkaz vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, které se nachází serveru repliky.  |
| jméno | mydemoreplicaserver | Název na zastavení replikace na serveru repliky. |

### <a name="delete-a-replica-server"></a>Odstranění serveru repliky

Odstranění serveru repliky pro čtení se dá provést spuštěním příkazu **[AZ MariaDB Server Delete](/cli/azure/mariadb/server)** .

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Odstranit hlavního serveru

> [!IMPORTANT]
> Odstraňuje se hlavní server zastaví se replikace na všechny servery repliky a odstraní hlavní samotný server. Servery repliky se samostatnými servery, které nyní podporují čtení a zápisu.

Pokud chcete odstranit hlavní server, můžete spustit příkaz **[AZ MariaDB Server Delete](/cli/azure/mariadb/server)** .

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>REST API
Repliky pro čtení můžete vytvářet a spravovat pomocí [REST API Azure](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení
Repliku pro čtení můžete vytvořit pomocí [rozhraní API pro vytvoření](/rest/api/mariadb/servers/create):

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Další informace o tom, které oblasti můžete vytvořit repliku v, najdete v [článku věnovaném konceptům pro čtení replik](concepts-read-replicas.md). 

Pokud jste `azure.replication_support` nenastavili parametr na repliku na pro obecné účely nebo paměťově optimalizovaném hlavním serveru a server restartovali, zobrazí se chyba. Před vytvořením repliky tyto dva kroky proveďte.

Replika se vytvoří pomocí stejného nastavení výpočtů a úložiště jako hlavní. Po vytvoření repliky se dá několik nastavení měnit nezávisle na hlavním serveru: generování výpočetních prostředků, virtuální jádra, úložiště a doba uchovávání záloh. Cenová úroveň se dá změnit také nezávisle, s výjimkou nebo z úrovně Basic.


> [!IMPORTANT]
> Než bude nastavení hlavního serveru aktualizováno na novou hodnotu, aktualizujte nastavení repliky na hodnotu rovná se nebo větší. Tato akce pomůže replice uchovávat všechny změny provedené v hlavní větvi.

### <a name="list-replicas"></a>Vypsat repliky
Seznam replik hlavního serveru můžete zobrazit pomocí [rozhraní API seznamu replik](/rest/api/mariadb/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Zastavuje se replikace na serveru repliky
Replikaci mezi hlavním serverem a replikou pro čtení můžete zastavit pomocí [rozhraní API pro aktualizaci](/rest/api/mariadb/servers/update).

Po zastavení replikace na hlavní server a repliku pro čtení ji nejde vrátit zpět. Replika čtení se stal samostatným serverem, který podporuje čtení i zápis. Samostatný server se nedá znovu vytvořit do repliky.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Odstranění hlavního serveru nebo serveru repliky
K odstranění hlavního serveru nebo serveru repliky použijte [rozhraní API pro odstranění](/rest/api/mariadb/servers/delete):

Při odstranění hlavního serveru se zastaví replikace do všech replik čtení. Repliky čtení se stanou samostatnými servery, které nyní podporují čtení i zápis.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Další kroky

- Další informace o [čtení replik](concepts-read-replicas.md)
