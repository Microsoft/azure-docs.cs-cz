---
title: 'Rychlý start: Vytvoření serveru Azure Database for MariaDB – Azure CLI'
description: Tento rychlý start popisuje, jak použít Azure CLI k vytvoření serveru Azure Database for MariaDB ve skupině prostředků Azure.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: d500a5cab4373d21b729a177ef847c40c2f4211b
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49354015"
---
# <a name="create-an-azure-database-for-mariadb-server-by-using-the-azure-cli"></a>Vytvoření serveru Azure Database for MariaDB pomocí Azure CLI

Pomocí Azure CLI můžete vytvářet a spravovat prostředky Azure z příkazového řádku nebo ve skriptech. Tento rychlý start popisuje, jak za pět minut vytvořit pomocí Azure CLI server Azure Database for MariaDB ve skupině prostředků Azure. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud používáte místní instalaci rozhraní příkazového řádku, musíte mít Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Pokud máte více předplatných, zvolte předplatné, které obsahuje prostředek nebo ve kterém se vám prostředek účtuje. Pokud chcete vybrat ID konkrétního předplatného ve vašem účtu, použijte příkaz [az account set](/cli/azure/account#az-account-set):

```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina.

Následující příklad vytvoří skupinu prostředků `myresourcegroup` v umístění `westus`:

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Vytvoření serveru Azure Database for MariaDB

Server Azure Database for MariaDB vytvoříte pomocí příkazu [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create). Server může spravovat více databází. Obvykle se pro jednotlivé projekty nebo uživatele používají samostatné databáze.

Nastavení | Ukázková hodnota | Popis
---|---|---
jméno | **mydemoserver** | Zadejte jedinečný název, který identifikuje váš server Azure Database for MariaDB. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí mít 3 až 63 znaků.
resource-group | **myresourcegroup** | Zadejte název skupiny prostředků Azure.
sku-name | **GP_Gen5_2** | Název skladové položky. Dodržuje konvenci *cenová úroveň*\_*výpočetní generace*\_*počet virtuálních jader* ve zkráceném zápisu. Další informace o parametru **sku-name** najdete v části pod touto tabulkou.
backup-retention | **7** | Určuje, jak dlouho se mají uchovávat zálohy. Jednotkou jsou dny. Rozsah: 7 až 35. 
geo-redundant-backup | **Disabled** (Zakázáno) | Určuje, jestli pro tento server mají být povolené geograficky redundantní zálohy. Povolené hodnoty: **Enabled** (Povoleno), **Disabled** (Zakázáno).
location | **westus** | Lokace Azure pro server.
ssl-enforcement | **Enabled** (Povoleno) | Určuje, jestli pro tento server má být povolený protokol SSL. Povolené hodnoty: **Enabled** (Povoleno), **Disabled** (Zakázáno).
velikost úložiště | **51200** | Kapacita úložiště serveru (jednotkou jsou megabajty). Platné velikosti úložiště jsou 5 120 MB (minimum) a vyšší s přírůstky po 1 024 MB. Další informace o omezeních velikosti úložiště najdete v tématu [Cenové úrovně](./concepts-pricing-tiers.md). 
version | **10.2** | Hlavní verze modulu MariaDB
admin-user | **myadmin** | Uživatelské jméno pro přihlášení správce. Parametr **admin-user** nemůže být **azure_superuser**, **admin**, **administrator**, **root**, **guest** ani **public**.
admin-password | *Vaše heslo* | Heslo uživatele, který je správcem. Vaše heslo musí mít 8 až 128 znaků. Musí obsahovat znaky ze tří z těchto kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice a jiné než alfanumerické znaky.

Hodnota parametru **sku-name** má formát *cenová úroveň*\_*výpočetní generace*\_*počet virtuálních jader*, jak je znázorněno v následujících příkladech:
+ `--sku-name B_Gen5_4` se mapuje na cenovou úroveň Basic, výpočetní generaci Gen 5 a 4 virtuální jádra.
+ `--sku-name GP_Gen5_32` se mapuje na cenovou úroveň pro obecné účely, výpočetní generaci Gen 5 a 32 virtuálních jader.
+ `--sku-name MO_Gen5_2` se mapuje na cenovou úroveň optimalizovanou pro paměť, výpočetní generaci Gen 5 a 2 virtuální jádra.

Informace o platných hodnotách pro jednotlivé oblasti a úrovně najdete v tématu [Cenové úrovně](./concepts-pricing-tiers.md).

Následující příklad vytvoří server **mydemoserver** v oblasti USA – západ. Server je ve skupině prostředků **myresourcegroup** a má přihlašovací jméno správce serveru **myadmin**. Jedná se o server 5. generace na cenové úrovni pro obecné účely se 2 virtuálními jádry. Název serveru se mapuje na název DNS a v rámci Azure musí být globálně jedinečný. Nahraďte `<server_admin_password>` vlastním heslem správce serveru.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

## <a name="configure-a-firewall-rule"></a>Konfigurace pravidla brány firewall

Pravidlo brány firewall na úrovni serveru Azure Database for MariaDB vytvoříte pomocí příkazu [az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create). Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci, jako je například nástroj pro příkazový řádek mysql nebo MySQL Workbench, aby se k vašemu serveru připojila prostřednictvím brány firewall služby Azure Database for MariaDB. 

Následující příklad vytvoří pravidlo brány firewall `AllowMyIP`, které povolí připojení z konkrétní IP adresy 192.168.0.1. Použijte IP adresu nebo rozsah IP adres odpovídající umístění, ze kterého se připojujete. 

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Připojení ke službě Azure Database for MariaDB komunikují přes port 3306. Pokud se pokoušíte připojit z podnikové sítě, odchozí provoz přes port 3306 nemusí být povolený. V takovém případě se k serveru budete moct připojit pouze v případě, že vaše IT oddělení otevře port 3306.
> 

## <a name="configure-ssl-settings"></a>Konfigurace nastavení SSL

Ve výchozím nastavení se připojení SSL mezi serverem a klientskými aplikacemi vynucuje. Toto výchozí nastavení zajišťuje zabezpečení dat „v pohybu“ prostřednictvím šifrování datového proudu přes internet. Pro účely tohoto rychlého startu zakažte u svého serveru připojení SSL. Pro provozní servery se zakázání protokolu SSL nedoporučuje. Další informace najdete v článku o [konfiguraci připojení SSL v aplikaci pro zabezpečené připojení k Azure Database for MariaDB](./howto-configure-ssl.md).

Následující příklad na serveru Azure Database for MariaDB zakáže vynucování SSL:
 
```azurecli-interactive
az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
```

## <a name="get-connection-information"></a>Získání informací o připojení

Pokud se chcete připojit k serveru, budete muset zadat informace o hostiteli a přihlašovací údaje pro přístup. Informace o připojení získáte spuštěním následujícího příkazu:

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Výsledek je ve formátu JSON. Poznamenejte si hodnoty **fullyQualifiedDomainName** a **administratorLogin**.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-by-using-the-mysql-command-line-tool"></a>Připojení k serveru pomocí nástroje pro příkazový řádek mysql

Připojte se k serveru pomocí nástroje pro příkazový řádek mysql. Tento nástroj pro příkazový řádek si můžete [stáhnout](https://dev.mysql.com/downloads/) a nainstalovat do svého počítače. Přístup k tomuto nástroji pro příkazový řádek můžete získat také výběrem tlačítka **Vyzkoušet** u vzorových kódů v tomto článku. Dalším způsobem, jak získat přístup k tomuto nástroji pro příkazový řádek, je otevřít **Azure Cloud Shell** výběrem tlačítka **>_** na pravém horním panelu nástrojů na webu Azure Portal.

Připojení k serveru pomocí nástroje pro příkazový řádek mysql:

1. Připojení k serveru:

  ```azurecli-interactive
  mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
  ```

2. Zobrazení stavu serveru na příkazovém řádku `mysql>`:

  ```sql
  status
  ```
  Měl by se zobrazit text podobný následujícímu:

  ```bash
  C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
  Enter password: ***********
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 65512
  Server version: 5.6.39.0 MariaDB Server

  Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

  mysql> status
  --------------
  mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

  Connection id:          64681
  Current database:
  Current user:           myadmin@40.118.201.21
  SSL:                    Cipher in use is AES256-SHA
  Current pager:          stdout
  Using outfile:          ''
  Using delimiter:        ;
  Server version:         5.6.39.0 MariaDB Server
  Protocol version:       10
  Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
  Server characterset:    latin1
  Db     characterset:    latin1
  Client characterset:    utf8
  Conn.  characterset:    utf8
  TCP port:               3306
  Uptime:                 1 day 3 hours 28 min 50 sec

  Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
  --------------

  mysql>
  ```

> [!TIP]
> Další příkazy najdete v [Referenční příručce k MySQL 5.7 – v kapitole 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Připojení k serveru pomocí aplikace MySQL Workbench

1.  Na klientském počítači otevřete MySQL Workbench. Pokud tato aplikace ještě není nainstalovaná, [stáhněte](https://dev.mysql.com/downloads/workbench/) a nainstalujte ji.

2.  V dialogovém okně pro **nastavení nového připojení** zadejte na kartě **Parametry** následující informace:

 ![Nastavení nového připojení](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

  | Nastavení | Navrhovaná hodnota | Popis |
  |---|---|---|
  | Název připojení | **Ukázkové připojení** | Zadejte popisek tohoto připojení (název připojení může být libovolný). |
  | Způsob připojení | **Standard (TCP/IP)** | Pro připojení k Azure Database for MariaDB použijte protokol TCP/IP. |
  | Název hostitele | **mydemoserver.mariadb.database.azure.com** | Název serveru, který jste si poznamenali dříve. |
  | Port | **3306** | Výchozí port pro Azure Database for MariaDB. |
  | Uživatelské jméno | **myadmin@mydemoserver** | Přihlašovací jméno správce serveru, které jste si poznamenali dříve. |
  | Heslo | *Vaše heslo* | Použijte heslo účtu správce, které jste nastavili dříve. |

3. Pokud chcete zkontrolovat, jestli jsou všechny parametry správně nakonfigurované, vyberte **Test připojení**.

4. Výběrem tohoto připojení se úspěšně připojte k serveru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud prostředky použité v tomto rychlém startu nepotřebujete pro další rychlý start nebo kurz, můžete je odstranit spuštěním následujícího příkazu: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Pokud chcete odstranit pouze server, který jste vytvořili v tomto rychlém startu, spusťte příkaz [az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete):

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

<!--
## Next steps

> [!div class="nextstepaction"]
> [Design a MariaDB Database with Azure CLI](./tutorial-design-database-using-cli.md)
-->