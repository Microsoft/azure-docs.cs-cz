---
title: Správa replik čtení – Azure CLI, REST API – Azure Database for MySQL
description: Zjistěte, jak nastavit a spravovat repliky čtení v Azure Database for MySQL pomocí rozhraní API Azure nebo rozhraní REST API.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ed57003c7a9a5a1a9d87aa2e8934af8c48b1d819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063334"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>Jak vytvořit a spravovat repliky čtení v Azure Database for MySQL pomocí rozhraní API Azure a rozhraní REST API

V tomto článku se dozvíte, jak vytvořit a spravovat repliky čtení ve službě Azure Database for MySQL pomocí rozhraní API Azure a rozhraní REST API. Další informace o replikách pro čtení naleznete v [přehledu](concepts-read-replicas.md).

## <a name="azure-cli"></a>Azure CLI
Můžete vytvářet a spravovat repliky čtení pomocí azure CLI.

### <a name="prerequisites"></a>Požadavky

- [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Databáze Azure pro mysql server,](quickstart-create-mysql-server-database-using-azure-portal.md) který se bude používat jako hlavní server. 

> [!IMPORTANT]
> Funkce repliky pro čtení je k dispozici jenom pro Azure Database pro servery MySQL v cenových úrovních s obecnou dostupností nebo optimalizací pro paměť. Ujistěte se, že hlavní server je v jedné z těchto cenových úrovní.

### <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

Server replik pro čtení lze vytvořit pomocí následujícího příkazu:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

Příkaz `az mysql server replica create` vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, pro kterou bude vytvořen replikovací server.  |
| jméno | mydemoreplicaserver | Název nového serveru repliky, který je vytvořen. |
| source-server | mydemoserver | Název nebo ID existujícího hlavního serveru, ze který má být replikován. |

Chcete-li vytvořit repliku pro `--location` čtení mezi oblastmi, použijte parametr. Příklad cli níže vytvoří repliku v západní USA.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Další informace o oblastech, ve kterých můžete vytvořit repliku, naleznete v [článku koncepty replik pro čtení](concepts-read-replicas.md). 

> [!NOTE]
> Repliky pro čtení jsou vytvořeny se stejnou konfigurací serveru jako předloha. Konfiguraci serveru repliklze změnit po jeho vytvoření. Doporučuje se, aby konfigurace serveru repliky byla udržována na stejných nebo vyšších hodnotách než hlavní server, aby byla replika schopna držet krok s předlohou.


### <a name="list-replicas-for-a-master-server"></a>Seznam replik pro hlavní server

Chcete-li zobrazit všechny repliky pro daný hlavní server, spusťte následující příkaz: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

Příkaz `az mysql server replica list` vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, pro kterou bude vytvořen replikovací server.  |
| název-serveru | mydemoserver | Název nebo ID hlavního serveru. |

### <a name="stop-replication-to-a-replica-server"></a>Zastavení replikace na replikační server

> [!IMPORTANT]
> Zastavení replikace na server je nevratné. Jakmile je replikace zastavena mezi předlohou a replikou, nelze ji vrátit zpět. Replika server se pak stane samostatný server a nyní podporuje čtení i zápisy. Tento server nelze znovu přeměnit na repliku.

Replikaci na server replik y pro čtení lze zastavit pomocí následujícího příkazu:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

Příkaz `az mysql server replica stop` vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, kde existuje replika server.  |
| jméno | mydemoreplicaserver | Název repliky serveru zastavit replikace na. |

### <a name="delete-a-replica-server"></a>Odstranění repliky serveru

Odstranění serveru repliky pro čtení lze provést spuštěním příkazu **[delete az mysql server.](/cli/azure/mysql/server)**

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Odstranění hlavního serveru

> [!IMPORTANT]
> Odstraněním hlavního serveru se zastaví replikace na všechny servery replik a odstraní se samotný hlavní server. Ze serverů replik se stanou samostatné servery, které teď podporují čtení i zápis.

Chcete-li odstranit hlavní server, můžete spustit příkaz **[az mysql server delete.](/cli/azure/mysql/server)**

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>REST API
Můžete vytvářet a spravovat repliky čtení pomocí [rozhraní Azure REST API](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení
Repliku pro čtení můžete vytvořit pomocí [rozhraní CREATE API](/rest/api/mysql/servers/create):

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
> Další informace o oblastech, ve kterých můžete vytvořit repliku, naleznete v [článku koncepty replik pro čtení](concepts-read-replicas.md). 

Pokud jste nenastavili `azure.replication_support` parametr **replika** na serveru pro obecné účely nebo optimalizované pro paměť a restartovali server, zobrazí se chyba. Před vytvořením repliky proveďte tyto dva kroky.

Replika je vytvořena pomocí stejného nastavení výpočetních prostředků a úložiště jako předloha. Po vytvoření repliky lze změnit několik nastavení nezávisle na hlavním serveru: generování výpočetních prostředků, virtuální jádra, úložiště a záložní retenční období. Cenovou úroveň lze také změnit nezávisle, s výjimkou úrovně Basic nebo z ní.


> [!IMPORTANT]
> Před aktualizací nastavení hlavního serveru na novou hodnotu aktualizujte nastavení repliky na stejnou nebo vyšší hodnotu. Tato akce pomáhá replika držet krok s všechny změny provedené v předloze.

### <a name="list-replicas"></a>Seznam replik
Seznam replik hlavního serveru můžete zobrazit pomocí [rozhraní API seznamu replik](/rest/api/mysql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Zastavení replikace na replikační server
Replikaci mezi hlavním serverem a replikou pro čtení můžete zastavit pomocí [rozhraní API pro aktualizaci](/rest/api/mysql/servers/update).

Po zastavení replikace na hlavní server a repliku pro čtení ji nelze vrátit zpět. Replika pro čtení se stane samostatným serverem, který podporuje čtení i zápisy. Samostatný server nelze znovu vytvořit v replice.

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

### <a name="delete-a-master-or-replica-server"></a>Odstranění hlavního serveru nebo repliky serveru
Chcete-li odstranit hlavní nebo replikový server, použijte [rozhraní DELETE API](/rest/api/mysql/servers/delete):

Při odstranění hlavního serveru je zastavena replikace všech replik čtení. Čtení repliky stát samostatné servery, které nyní podporují čtení i zápisy.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Další kroky

- Další informace o [čtení replik](concepts-read-replicas.md)
