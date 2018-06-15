---
title: 'Rychlý start: Vytvoření serveru Azure Database for MySQL – Azure CLI'
description: Tento rychlý start popisuje, jak použít rozhraní příkazového řádku Azure k vytvoření serveru Azure Database for MySQL ve skupině prostředků Azure.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 04/01/2018
ms.custom: mvc
ms.openlocfilehash: 2fc87bb61085142a06a99d5619a47a82b29c802c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266614"
---
# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Vytvoření serveru Azure Database for MySQL pomocí Azure CLI
Tento rychlý start popisuje, jak za pět minut vytvořit pomocí Azure CLI server Azure Database for MySQL ve skupině prostředků Azure. Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém tento prostředek existuje nebo ve kterém se fakturuje. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account#az_account_set) určité ID předplatného.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte [skupinu prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina.

Následující příklad vytvoří skupinu prostředků s názvem `myresourcegroup` v umístění `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Vytvoření serveru Azure Database for MySQL
Vytvořte server Azure Database for MySQL pomocí příkazu **[az mysql server create](/cli/azure/mysql/server#az_mysql_server_create)**. Server může spravovat více databází. Obvykle se pro jednotlivé projekty nebo uživatele používají samostatné databáze.

Následující příklad vytvoří server `mydemoserver` v umístění USA – západ, ve skupině prostředků `myresourcegroup` a s přihlašovacím jménem správce serveru `myadmin`. Jedná se o server pro **Obecné účely** **Gen 4** se 2 **virtuálními jádry**. Název serveru se mapuje na název DNS, a proto musí být v rámci Azure globálně jedinečný. Nahraďte položku `<server_admin_password>` vlastní hodnotou.
```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```

## <a name="configure-firewall-rule"></a>Konfigurace pravidla brány firewall
Vytvořte pravidlo brány firewall na úrovni serveru pro Azure Database for MySQL pomocí příkazu **[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create)**. Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci, jako je například nástroj příkazového řádku **mysql.exe** nebo MySQL Workbench, aby se k vašemu serveru připojila prostřednictvím brány firewall služby Azure MySQL. 

Následující příklad vytvoří pravidlo brány firewall pro předdefinovaný rozsah adres, který je v tomto příkladu tvořen celým možným rozsahem IP adres.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
Povolení všech IP adres není bezpečné. Tento příklad je uvedený pro zjednodušení, ale ve skutečném scénáři musíte znát přesné rozsahy IP adres, které máte přidat pro své aplikace a uživatele. 

> [!NOTE]
> Připojení ke službě Azure Database for MySQL komunikují přes port 3306. Pokud se pokoušíte připojit z podnikové sítě, odchozí provoz přes port 3306 nemusí být povolený. V takovém případě se k serveru nemůžete připojit, dokud vaše IT oddělení neotevře port 3306.
> 


## <a name="configure-ssl-settings"></a>Konfigurace nastavení SSL
Ve výchozím nastavení se připojení SSL mezi serverem a klientskými aplikacemi vynucuje. Toto výchozí nastavení zajišťuje zabezpečení dat „v pohybu“ prostřednictvím šifrování datového proudu přes internet. Pro zjednodušení tohoto rychlého startu zakažte u svého serveru připojení SSL. Pro provozní servery se zakázání protokolu SSL nedoporučuje. Další informace najdete v tématu [Konfigurace připojení SSL v aplikaci pro zabezpečené připojení k Azure Database for MySQL](./howto-configure-ssl.md).

Následující příklad zakazuje vynucování SSL na serveru MySQL.
 
 ```azurecli-interactive
 az mysql server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Získání informací o připojení

Pokud se chcete připojit k serveru, budete muset zadat informace o hostiteli a přihlašovací údaje pro přístup.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Výsledek je ve formátu JSON. Poznamenejte si **fullyQualifiedDomainName** a **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>Připojení k serveru pomocí nástroje příkazového řádku mysql.exe
Připojte se k serveru pomocí nástroje příkazového řádku **mysql.exe**. MySQL můžete stáhnout [odsud](https://dev.mysql.com/downloads/) a nainstalovat do svého počítače. Místo toho můžete také kliknout na tlačítko **Vyzkoušet** ve vzorových kódech nebo na tlačítko `>_` v pravém horním panelu nástrojů na webu Azure Portal a spustit **Azure Cloud Shell**.

Zadejte další příkazy: 

1. Připojení k serveru pomocí nástroje příkazového řádku **mysql**:
```azurecli-interactive
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

2. Zobrazení stavu serveru:
```sql
 mysql> status
```
Pokud vše půjde dobře, měl by výstupem nástroje příkazového řádku být následující text:

```dos
C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> Další příkazy najdete v [Referenční příručce k MySQL 5.7 – v kapitole 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Připojení k serveru pomocí nástroje grafického uživatelského rozhraní MySQL Workbench
1.  Na klientském počítači spusťte aplikaci MySQL Workbench. MySQL Workbench můžete stáhnout a nainstalovat [odtud](https://dev.mysql.com/downloads/workbench/).

2.  V dialogovém okně pro **nastavení nového připojení** zadejte na kartě **Parametry** následující informace:

   ![nastavení nového připojení](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

| **Nastavení** | **Navrhovaná hodnota** | **Popis** |
|---|---|---|
|   Název připojení | My Connection | Zadejte jmenovku pro toto připojení (může být libovolná). |
| Způsob připojení | zvolte Standardní (TCP/IP) | Pro připojení k Azure Database for MySQL použijte protokol TCP/IP. |
| Název hostitele | mydemoserver.mysql.database.azure.com | Název serveru, který jste si předtím poznamenali. |
| Port | 3306 | Použije se výchozí port pro MySQL. |
| Uživatelské jméno | myadmin@mydemoserver | Přihlašovací jméno správce serveru, které jste si předtím poznamenali. |
| Heslo | **** | Použijte heslo správce, které jste nakonfigurovali v předchozích krocích. |

Pokud chcete otestovat, jestli jsou všechny parametry správně nakonfigurované, klikněte na **Test připojení**.
Teď se můžete kliknutím na toto připojení úspěšně připojit k serveru.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud tyto prostředky nepotřebujete pro další rychlý start nebo kurz, můžete je pomocí následujícího příkazu odstranit: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Pokud chcete odstranit jenom nově vytvořený server, můžete spustit příkaz **[az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete)**.
```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Návrh databáze MySQL pomocí rozhraní příkazového řádku Azure](./tutorial-design-database-using-cli.md)
