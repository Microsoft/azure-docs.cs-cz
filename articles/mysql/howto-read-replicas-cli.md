---
title: Vytvoření a správa repliky pro čtení ve službě Azure Database for MySQL
description: Tento článek popisuje, jak nastavit a spravovat repliky pro čtení ve službě Azure Database for MySQL pomocí Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: ba8af55f7467e361136e4b0c57c97b4fa187cec0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66304962"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli"></a>Jak vytvořit a spravovat čtení replik ve službě Azure Database for MySQL pomocí Azure CLI

V tomto článku se dozvíte, jak vytvořit a spravovat čtení replik v rámci stejné oblasti Azure jako hlavní v služba Azure Database for MySQL pomocí Azure CLI.

> [!IMPORTANT]
> Čtení repliky můžete vytvořit ve stejné oblasti jako váš hlavní server, nebo v libovolné jiné oblasti Azure podle vašeho výběru. Replikace mezi oblastmi je aktuálně ve verzi public preview.

## <a name="prerequisites"></a>Požadavky

- [Nainstalujte Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- [– Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-portal.md) , který se použije jako hlavní server. 

> [!IMPORTANT]
> Čtení replik funkce dostupná jenom pro službu Azure Database pro servery MySQL v obecné účely nebo k paměťově optimalizovaným cenové úrovně. Ujistěte se, že se že hlavní server je v jednom z těchto cenové úrovně.

## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

Server repliky pro čtení můžete vytvořit pomocí následujícího příkazu:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mysql server replica create` Příkaz vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, ve kterém server repliky bude vytvořena.  |
| jméno | mydemoreplicaserver | Název nového serveru repliky, který je vytvořen. |
| source-server | mydemoserver | Název nebo ID existující hlavní server pro replikaci z. |

Chcete-li vytvořit křížových přečíst oblasti repliky, použijte `--location` parametru. Následující příklad rozhraní příkazového řádku vytvoří replika v oblasti západní USA.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Repliky pro čtení jsou vytvořeny se stejnou konfigurací serveru na hlavní server. Konfigurace serveru repliky můžete po jejím vytvoření změnit. Doporučuje se, že konfigurace serveru repliky by udržováno na hodnoty roven nebo větší než hlavní Ujistěte se, že je replika schopné udržovat tempo s hlavní.

## <a name="stop-replication-to-a-replica-server"></a>Zastavuje se replikace na serveru repliky

> [!IMPORTANT]
> Zastavuje se replikace na server je nevratná operace. Jakmile se zastaví replikace mezi hlavní a repliky, nejde vrátit. Server repliky pak stane samostatným serverem a nyní podporuje čtení a zápisu. Tento server nelze je převést na repliku znovu.

Replikace na server repliky pro čtení lze zastavit pomocí následujícího příkazu:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

`az mysql server replica stop` Příkaz vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, které se nachází serveru repliky.  |
| jméno | mydemoreplicaserver | Název na zastavení replikace na serveru repliky. |

## <a name="delete-a-replica-server"></a>Odstranění serveru repliky

Odstraňuje se stal serverem repliky pro čtení, můžete provést spuštěním **[az mysql server delete](/cli/azure/mysql/server)** příkazu.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>Odstranit hlavního serveru

> [!IMPORTANT]
> Odstraňuje se hlavní server zastaví se replikace na všechny servery repliky a odstraní hlavní samotný server. Servery repliky se samostatnými servery, které nyní podporují čtení a zápisu.

Pokud chcete odstranit hlavní server, můžete spustit **[az mysql server delete](/cli/azure/mysql/server)** příkazu.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>Seznam replik pro hlavní server

Chcete-li zobrazit všechny repliky pro danou hlavní server, spusťte následující příkaz: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mysql server replica list` Příkaz vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, ve kterém server repliky bude vytvořena.  |
| název-serveru | mydemoserver | Název nebo ID hlavního serveru. |

## <a name="next-steps"></a>Další postup

- Další informace o [čtení replik](concepts-read-replicas.md)
