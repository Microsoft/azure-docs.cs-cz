---
title: 'Rychlý Start: vytvoření synapse fondu SQL pomocí Azure CLI'
description: Rychle vytvořte synapse fond SQL s pravidlem brány firewall na úrovni serveru s použitím rozhraní příkazového řádku Azure CLI.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.custom: azure-synapse, devx-track-azurecli
ms.openlocfilehash: 712405ec7ba61e05bb587efc3e6393192d820e97
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865472"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-cli"></a>Rychlý Start: vytvoření synapse fondu SQL pomocí Azure CLI

Vytvořte synapse fond SQL (datový sklad) ve službě Azure synapse Analytics pomocí Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="getting-started"></a>Začínáme

Pomocí těchto příkazů se přihlaste k Azure a nastavte skupinu prostředků.

1. Pokud používáte místní instalaci, přihlaste se k Azure spuštěním příkazu [AZ Login](/cli/azure/reference-index#az_login) :

   ```azurecli
   az login
   ```

1. V případě potřeby pomocí příkazu [AZ Account set](/cli/azure/account#az_account_set) vyberte vaše předplatné:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Spuštěním příkazu [AZ Group Create](/cli/azure/group#az_group_create) vytvořte skupinu prostředků:

   ```azurecli
   az group create --name myResourceGroup --location WestEurope
   ```

1. Pomocí příkazu [AZ SQL Server Create](/cli/azure/sql/server#az_sql_server_create) vytvořte [logický SQL Server](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) :

   ```azurecli
   az sql server create --resource-group myResourceGroup --name mysqlserver \
      --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
   ```

   Server obsahuje soubor databází spravovaných jako skupina.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurace pravidla brány firewall na úrovni serveru

Vytvořte [pravidlo brány firewall na úrovni serveru](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci, jako je například SQL Server Management Studio nebo nástroj SQLCMD, připojit se ke fondu SQL prostřednictvím brány firewall služby fondu SQL.

Pomocí příkazu [AZ SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) vytvořte pravidlo brány firewall:

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --name AllowSome \
   --server mysqlserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

V tomto příkladu je brána firewall otevřená jenom pro jiné prostředky Azure. Pokud chcete povolit externí připojení, změňte IP adresu na příslušnou adresu pro vaše prostředí. Chcete-li otevřít všechny IP adresy, použijte jako počáteční IP adresu 0.0.0.0 a jako koncovou adresu 255.255.255.255.

> [!NOTE]
> Koncových bodů SQL komunikuje přes port 1433. Pokud se pokoušíte připojit z podnikové sítě, nemusí být odchozí provoz přes port 1433 bránou firewall vaší sítě povolený. Pokud ano, nebudete se moct připojit k serveru, dokud vaše IT oddělení neotevře port 1433.
>

## <a name="create-and-manage-your-sql-pool"></a>Vytvoření a Správa fondu SQL

Vytvořte fond SQL. V tomto příkladu se jako cíl služby používá DW100c, což je výchozí bod nižší ceny pro váš fond SQL.

> [!NOTE]
> Budete potřebovat dříve vytvořený pracovní prostor. Další informace najdete v tématu [rychlý Start: vytvoření pracovního prostoru Azure synapse pomocí Azure CLI](../quickstart-create-workspace-cli.md).

Pomocí příkazu [AZ synapse SQL Pool Create](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_create) vytvořte fond SQL:

```azurecli
az synapse sql pool create --resource-group myResourceGroup --name mySampleDataWarehouse \
   --performance-level "DW1000c" --workspace-name testsynapseworkspace
```

Další informace o možnostech parametrů najdete v tématu [AZ synapse SQL Pool](/cli/azure/synapse/sql/pool).

Fondy SQL můžete zobrazit pomocí příkazu [AZ synapse SQL Pool list](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_list) :

```azurecli
az synapse sql pool list --resource-group myResourceGroup --workspace-name testsynapseworkspace
```

Pomocí příkazu [AZ synapse SQL Pool Update](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_update) aktualizujte stávající fond:

```azurecli
az synapse sql pool update --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Pro pozastavení fondu použijte příkaz [AZ synapse SQL Pool Pause](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_pause) :

```azurecli
az synapse sql pool pause --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Pomocí příkazu [AZ synapse SQL Pool Resume](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_resume) spusťte pozastavený fond:

```azurecli
az synapse sql pool resume --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Pokud chcete odebrat existující fond SQL, použijte příkaz [AZ synapse SQL Pool Delete](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_delete) :

```azurecli
az synapse sql pool delete --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další kurzy Rychlý start v této kolekci vycházejí z tohoto rychlého startu.

> [!TIP]
> Pokud budete chtít pokračovat v práci s novějšími kurzy rychlý Start, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí příkazu [AZ Group Delete](/cli/azure/group#az_group_delete) odstraňte všechny prostředky vytvořené tímto rychlým startem.
>

```azurecli
az group delete --ResourceGroupName MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

Nyní jste vytvořili fond SQL, vytvořili jste pravidlo brány firewall a připojili jste se k vašemu fondu SQL. Pokud se chcete dozvědět víc, přejděte k článku o [načtení dat do fondu SQL](./load-data-from-azure-blob-storage-using-copy.md) .
