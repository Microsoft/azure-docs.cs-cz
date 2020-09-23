---
title: Konfigurace parametrů – Azure Database for PostgreSQL-flexibilní Server
description: Tento článek popisuje, jak nakonfigurovat parametry Postgres v Azure Database for PostgreSQL-flexibilním serveru pomocí Azure CLI.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 43b14858cc99cac41e277b03171fd4cac4d6eafa
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936774"
---
# <a name="customize-server-parameters-for-azure-database-for-postgresql---flexible-server-using-azure-cli"></a>Přizpůsobení parametrů serveru pro Azure Database for PostgreSQL-flexibilní Server pomocí Azure CLI

Můžete vypsat, zobrazit a aktualizovat parametry konfigurace pro server Azure PostgreSQL pomocí rozhraní příkazového řádku (Azure CLI). Podmnožina parametrů modulu je vystavena na úrovni serveru a lze ji upravit. 

## <a name="prerequisites"></a>Požadavky

Pokud chcete projít tento průvodce, budete potřebovat:
- Vytvoření serveru a databáze Azure Database for PostgreSQL pomocí následujících kroků vytvořte [Azure Database for PostgreSQL](quickstart-create-server-cli.md)
- Nainstalujte na svém počítači rozhraní příkazového řádku [Azure CLI](/cli/azure/install-azure-cli) nebo použijte [Azure Cloud Shell](../../cloud-shell/overview.md) v Azure Portal pomocí prohlížeče.

## <a name="list-server-parameters-for-a-flexible-server"></a>Výpis parametrů serveru pro flexibilní Server

Pokud chcete zobrazit seznam všech parametrů, které lze upravovat na serveru a jejich hodnotách, spusťte příkaz [AZ Postgres Flexible-Server Parameter list](/cli/azure/postgres/flexible-server/parameter) .

Můžete zobrazit seznam parametrů serveru pro server **mydemoserver.Postgres.Database.Azure.com** v části Skupina prostředků **myresourcegroup**.

```azurecli-interactive
az postgres flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="show-server-parameter-details"></a>Zobrazit podrobnosti parametru serveru

Chcete-li zobrazit podrobnosti o konkrétním parametru serveru, spusťte příkaz [AZ Postgres Flexible-Server Parameter show](/cli/azure/postgres/flexible-server/parameter)  .

Tento příklad ukazuje podrobnosti parametru serveru **pro \_ zprávy o minimálních \_ zprávách** pro server **mydemoserver.Postgres.Database.Azure.com** v části Skupina prostředků **myresourcegroup.**

```azurecli-interactive
az postgres flexible-server parameter show --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="modify-server-parameter-value"></a>Upravit hodnotu parametru serveru

Můžete také změnit hodnotu určitého parametru serveru, která aktualizuje základní konfigurační hodnotu pro modul PostgreSQL serveru. Chcete-li aktualizovat parametr, použijte příkaz [AZ Postgres flexibilní-Server Parameter set](/cli/azure/postgres/flexible-server/parameter) . 

Chcete-li aktualizovat parametr serveru pro ** \_ minimální \_ zprávy s protokolem** serveru **mydemoserver.Postgres.Database.Azure.com** v části Skupina prostředků **myresourcegroup.**

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --value INFO --resource-group myresourcegroup --server-name mydemoserver
```

Pokud chcete resetovat hodnotu parametru, stačí zvolit volitelný `--value` parametr a služba použije výchozí hodnotu. Ve výše uvedeném příkladu by vypadalo takto:

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

Tento příkaz obnoví parametr pro ** \_ minimální počet \_ zpráv protokolu** na **Upozornění**na výchozí hodnotu. Další informace o parametrech serveru a přípustných hodnotách naleznete v dokumentaci PostgreSQL o [Nastavení parametrů](https://www.postgresql.org/docs/12/config-setting.html).

## <a name="next-steps"></a>Další kroky

- Konfigurace a přístup k protokolům serveru najdete v tématu [protokoly serveru v nástroji Azure Database for PostgreSQL](concepts-logging.md)
