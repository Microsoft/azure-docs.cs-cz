---
title: Spravovat další repliky pro službu Azure Database for PostgreSQL – jeden Server z příkazového řádku Azure
description: Další informace o správě repliky pro čtení ve službě Azure Database for PostgreSQL – jeden Server z příkazového řádku Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: 9a6a1a744a8441d2f082d4d14a3aba8aa1cfc09e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66306021"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Vytvoření a správa repliky pro čtení z příkazového řádku Azure

V tomto článku se dozvíte, jak vytvářet a spravovat repliky pro čtení ve službě Azure Database for PostgreSQL z příkazového řádku Azure. Další informace o čtení replik, najdete v článku [přehled](concepts-read-replicas.md).

> [!IMPORTANT]
> Čtení repliky můžete vytvořit ve stejné oblasti jako váš hlavní server, nebo v libovolné jiné oblasti Azure podle vašeho výběru. Replikace mezi oblastmi je aktuálně ve verzi public preview.

## <a name="prerequisites"></a>Požadavky
- [– Azure Database for PostgreSQL server](quickstart-create-server-up-azure-cli.md) na hlavní server.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Pokud chcete zjistit nainstalovanou verzi, spusťte příkaz `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 


## <a name="prepare-the-master-server"></a>Příprava hlavního serveru
Tyto kroky musí použije k přípravě na úrovni obecné účely nebo k paměťově optimalizovaným hlavní server.

`azure.replication_support` Parametr musí být nastaven na **REPLIKY** na hlavní server. Při změně této statický parametr je nutné tato změna se projeví restartovat server.

1. Nastavte `azure.replication_support` do REPLIKY.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Restartujte na použití změny na server.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>Vytvoření repliky pro čtení

[Az postgres server repliky vytvořit](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) příkaz vyžaduje následující parametry:

| Nastavení | Příklad hodnoty | Popis  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Skupina prostředků, ve kterém se vytvoří serveru repliky.  |
| name | mydemoserver-replica | Název nového serveru repliky, který je vytvořen. |
| source-server | mydemoserver | Název nebo prostředek ID existující hlavní server pro replikaci z. |

V následujícím příkladu rozhraní příkazového řádku je replika vytvořena ve stejné oblasti jako hlavní server.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Chcete-li vytvořit křížových přečíst oblasti repliky, použijte `--location` parametru. Následující příklad rozhraní příkazového řádku vytvoří replika v oblasti západní USA.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

Pokud jste nenastavili `azure.replication_support` parametr **REPLIKY** na obecné účely nebo k paměťově optimalizovaným hlavního serveru a restartujte server, obdržíte chybu. Před vytvořením repliky proveďte tyto dva kroky.

Replika je vytvořen pomocí stejné konfigurace serveru na hlavní server. Po vytvoření repliky několik nastavení lze změnit nezávisle z hlavního serveru: výpočetní generace, virtuální jádra, úložiště a dobu uchování zálohování. Cenovou úroveň můžete změnit také nezávisle na sobě, s výjimkou do nebo z úrovně Basic.

> [!IMPORTANT]
> Před hlavním serverem služby konfigurace se aktualizuje na nové hodnoty, aktualizujte konfiguraci repliky na stejné nebo vyšší hodnoty. Tato akce zajistí, že replika je dokáže dodat se změnami provedenými na hlavní server.

## <a name="list-replicas"></a>Seznam replik
Seznam replik hlavního serveru můžete zobrazit pomocí [az postgres server repliky seznamu](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) příkazu.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>Zastavuje se replikace na serveru repliky
Replikace mezi serverem a hlavním serverem repliky pro čtení můžete zastavit pomocí [az postgres server repliky stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) příkazu.

Po zastavení replikace do hlavního serveru a repliky pro čtení nejde vrátit. Čtení replika přestane být samostatný server, který podporuje operace čtení i zápisu. Samostatný server nelze je převést na repliku znovu.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>Odstranění serveru master a repliky
Postup odstranění serveru hlavním uzlem nebo replik, můžete použít [az postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) příkazu.

Když odstraníte hlavní server, se zastaví replikace na všechny repliky pro čtení. Čtení replik se stanou samostatné servery, které nyní podporují čtení a zápisu.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Další postup
Další informace o [čtení replik ve službě Azure Database for PostgreSQL](concepts-read-replicas.md).