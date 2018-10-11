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
ms.openlocfilehash: 9e87dacb80aa7fc5f5b073e631fc06ae74b3ad00
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996600"
---
# <a name="create-an-azure-database-for-mariadb-server-using-azure-cli"></a>Vytvoření serveru Azure Database for MariaDB pomocí Azure CLI
Tento rychlý start popisuje, jak za pět minut vytvořit pomocí Azure CLI server Azure Database for MariaDB ve skupině prostředků Azure. Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém tento prostředek existuje nebo ve kterém se fakturuje. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account#az-account-set) určité ID předplatného.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte [skupinu prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina.

Následující příklad vytvoří skupinu prostředků s názvem `myresourcegroup` v umístění `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Vytvoření serveru Azure Database for MariaDB
Server Azure Database for MariaDB vytvoříte pomocí příkazu **[az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create)**. Server může spravovat více databází. Obvykle se pro jednotlivé projekty nebo uživatele používají samostatné databáze.

**Nastavení** | **Ukázková hodnota** | **Popis**
---|---|---
name | mydemoserver | Zvolte jedinečný název, který identifikuje váš server Azure Database for MariaDB. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí se skládat ze 3 až 63 znaků.
resource-group | myresourcegroup | Zadejte název skupiny prostředků Azure.
sku-name | GP_Gen5_2 | Název skladové jednotky. Dodržuje konvenci {cenová úroveň}_{výpočetní generace}_{virtuální jádra} ve zkráceném zápisu. Další informace o parametru sku-name najdete pod touto tabulkou.
backup-retention | 7 | Určuje, jak dlouho se mají uchovávat zálohy. Jednotkou jsou dny. Rozsah je 7 až 35. 
geo-redundant-backup | Disabled | Určuje, jestli pro tento server mají nebo nemají být povolené geograficky redundantní zálohy. Povolené hodnoty: Enabled, Disabled
location | westus | Umístění serveru v Azure.
ssl-enforcement | Enabled | Určuje, jestli pro tento server má nebo nemá být povolený protokol SSL. Povolené hodnoty: Enabled, Disabled
storage-size | 51200 | Kapacita úložiště serveru (jednotkou jsou megabajty). Platná velikost úložiště je minimálně 5 120 MB a navyšuje se v přírůstcích 1 024 MB. Další informace o limitech velikosti úložiště najdete v dokumentu o [cenových úrovních](./concepts-pricing-tiers.md). 
version | 10.2 | Hlavní verze modulu MariaDB
admin-user | myadmin | Uživatelské jméno pro přihlášení správce. Nesmí to být **azure_superuser**, **admin**, **administrator**, **root**, **guest** ani **public**.
admin-password | Password123 | Heslo uživatele, který je správcem. Musí mít 8 až 128 znaků. Heslo musí obsahovat znaky ze tří z těchto kategorií: velká písmena anglické abecedy, malá písmena anglické abecedy, číslice a jiné než alfanumerické znaky.

Hodnota parametru sku-name má formát {cenová_úroveň}\_{výpočetní_generace}\_{počet_virtuálních_jader} jako v následujících příkladech:
+ `--sku-name B_Gen5_4` se mapuje na úroveň Basic 5. generace se 4 virtuálními jádry.
+ `--sku-name GP_Gen5_32` se mapuje na úroveň pro obecné účely 5. generace se 32 virtuálními jádry.
+ `--sku-name MO_Gen5_2` se mapuje na úroveň optimalizovanou pro paměť 5. generace se 2 virtuálními jádry.

Vysvětlení platných hodnot pro jednotlivé oblasti a úrovně najdete v dokumentaci k [cenovým úrovním](./concepts-pricing-tiers.md).

Následující příklad vytvoří server `mydemoserver` v umístění USA – západ, ve skupině prostředků `myresourcegroup` a s přihlašovacím jménem správce serveru `myadmin`. Jedná se o **Univerzální** server **5. generace** se 2 **virtuálními jádry**. Název serveru se mapuje na název DNS, a proto musí být v rámci Azure globálně jedinečný. Nahraďte položku `<server_admin_password>` vlastní hodnotou.
```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

## <a name="configure-firewall-rule"></a>Konfigurace pravidla brány firewall
Pravidlo brány firewall na úrovni serveru Azure Database for MariaDB vytvoříte pomocí příkazu **[az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create)**. Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci, jako je například nástroj příkazového řádku **mysql** nebo MySQL Workbench, aby se k vašemu serveru připojila prostřednictvím brány firewall služby Azure MariaDB. 

Následující příklad vytvoří pravidlo brány firewall `AllowMyIP`, které povolí připojení z konkrétní IP adresy 192.168.0.1. Nahraďte ji za IP adresu nebo rozsah IP adres, které odpovídají umístění, ze kterého se budete připojovat. 

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Připojení ke službě Azure Database for MariaDB komunikují přes port 3306. Pokud se pokoušíte připojit z podnikové sítě, odchozí provoz přes port 3306 nemusí být povolený. V takovém případě se k serveru nemůžete připojit, dokud vaše IT oddělení neotevře port 3306.
> 

## <a name="configure-ssl-settings"></a>Konfigurace nastavení SSL
Ve výchozím nastavení se připojení SSL mezi serverem a klientskými aplikacemi vynucuje. Toto výchozí nastavení zajišťuje zabezpečení dat „v pohybu“ prostřednictvím šifrování datového proudu přes internet. Pro zjednodušení tohoto rychlého startu zakažte u svého serveru připojení SSL. Pro provozní servery se zakázání protokolu SSL nedoporučuje. Další informace najdete v článku o [konfiguraci připojení SSL v aplikaci pro zabezpečené připojení k Azure Database for MariaDB](./howto-configure-ssl.md).

Následující příklad zakazuje vynucování protokolu SSL na serveru MariaDB.
 
 ```azurecli-interactive
 az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Získání informací o připojení

Pokud se chcete připojit k serveru, budete muset zadat informace o hostiteli a přihlašovací údaje pro přístup.

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Výsledek je ve formátu JSON. Poznamenejte si **fullyQualifiedDomainName** a **administratorLogin**.
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

## <a name="connect-to-server-using-mysql-command-line"></a>Připojení k serveru pomocí příkazového řádku mysql
K serveru se můžete připojit pomocí nástroje příkazového řádku **mysql**. Tento nástroj příkazového řádku si můžete stáhnout [zde](https://dev.mysql.com/downloads/) a nainstalovat ho do počítače. Místo toho můžete také kliknout na tlačítko **Vyzkoušet** ve vzorových kódech nebo na tlačítko `>_` v pravém horním panelu nástrojů na webu Azure Portal a spustit **Azure Cloud Shell**.

Zadejte další příkazy: 

1. Připojení k serveru pomocí nástroje příkazového řádku **mysql**:
```azurecli-interactive
 mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

2. Zobrazení stavu serveru na příkazovém řádku mysql>:
```sql
status
```
Pokud vše půjde dobře, měl by výstupem nástroje příkazového řádku být následující text:

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

## <a name="connect-to-server-using-mysql-workbench"></a>Připojení k serveru pomocí aplikace MySQL Workbench
1.  Na klientském počítači spusťte aplikaci MySQL Workbench. MySQL Workbench můžete stáhnout a nainstalovat [odtud](https://dev.mysql.com/downloads/workbench/).

2.  V dialogovém okně pro **nastavení nového připojení** zadejte na kartě **Parametry** následující informace:

   ![nastavení nového připojení](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

| **Nastavení** | **Navrhovaná hodnota** | **Popis** |
|---|---|---|
|   Název připojení | My Connection | Zadejte jmenovku pro toto připojení (může být libovolná). |
| Způsob připojení | zvolte Standardní (TCP/IP) | Pro připojení k Azure Database for MariaDB použijte protokol TCP/IP. |
| Název hostitele | mydemoserver.mariadb.database.azure.com | Název serveru, který jste si předtím poznamenali. |
| Port | 3306 | Používá se výchozí port pro MariaDB. |
| Uživatelské jméno | myadmin@mydemoserver | Přihlašovací jméno správce serveru, které jste si předtím poznamenali. |
| Heslo | **** | Použijte heslo správce, které jste nakonfigurovali v předchozích krocích. |

Pokud chcete otestovat, jestli jsou všechny parametry správně nakonfigurované, klikněte na **Test připojení**.
Teď se můžete kliknutím na toto připojení úspěšně připojit k serveru.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud tyto prostředky nepotřebujete pro další rychlý start nebo kurz, můžete je pomocí následujícího příkazu odstranit: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Pokud chcete odstranit jenom nově vytvořený server, můžete spustit příkaz **[az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete)**.
```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

<!--
## Next steps

> [!div class="nextstepaction"]
> [Design a MariaDB Database with Azure CLI](./tutorial-design-database-using-cli.md)
-->