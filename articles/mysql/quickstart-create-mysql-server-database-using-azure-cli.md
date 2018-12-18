---
title: 'Rychlý start: Vytvoření Azure Database for MySQL server – rozhraní příkazového řádku Azure'
description: Tento rychlý start popisuje, jak použít rozhraní příkazového řádku Azure k vytvoření serveru Azure Database for MySQL ve skupině prostředků Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 11/01/2018
ms.custom: mvc
ms.openlocfilehash: 4c425e4da99774be5da8c060a8243bc34ec08f5a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547782"
---
# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Vytvoření serveru Azure Database for MySQL pomocí Azure CLI
Tento rychlý start popisuje, jak za pět minut vytvořit pomocí Azure CLI server Azure Database for MySQL ve skupině prostředků Azure. Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

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

## <a name="create-an-azure-database-for-mysql-server"></a>Vytvoření serveru Azure Database for MySQL
Vytvořte server Azure Database for MySQL pomocí příkazu **[az mysql server create](/cli/azure/mysql/server#az-mysql-server-create)**. Server může spravovat více databází. Obvykle se pro jednotlivé projekty nebo uživatele používají samostatné databáze.

**Nastavení** | **Ukázková hodnota** | **Popis**
---|---|---
jméno | mydemoserver | Zvolte jedinečný název serveru, který identifikuje váš server Azure Database for MySQL. Název serveru může obsahovat pouze malá písmena, číslice a znak spojovníku (-). Musí se skládat ze 3 až 63 znaků.
resource-group | myresourcegroup | Zadejte název skupiny prostředků Azure.
sku-name | GP_Gen4_2 | Název sku. Dodržuje konvenci {cenová úroveň}_{výpočetní generace}_{virtuální jádra} ve zkráceném zápisu. Další informace o parametru sku-name najdete pod touto tabulkou.
backup-retention | 7 | Určuje, jak dlouho se mají uchovávat zálohy. Jednotkou jsou dny. Rozsah je 7 až 35. 
geo-redundant-backup | Zakázáno | Určuje, jestli pro tento server mají nebo nemají být povolené geograficky redundantní zálohy. Povolené hodnoty: Povolený, zakázaný.
location | westus | Lokace Azure pro server.
ssl-enforcement | Povoleno | Určuje, jestli pro tento server má nebo nemá být povolený protokol SSL. Povolené hodnoty: Povolený, zakázaný.
velikost úložiště | 51200 | Kapacita úložiště serveru (jednotkou jsou megabajty). Platná velikost úložiště je minimálně 5 120 MB a navyšuje se v přírůstcích 1 024 MB. Další informace o limitech velikosti úložiště najdete v dokumentu o [cenových úrovních](./concepts-pricing-tiers.md). 
version | 5.7 | Hlavní verze MySQL.
admin-user | myadmin | Uživatelské jméno pro přihlášení správce. Nemůže být **azure_superuser**, **admin**, **administrátor**, **root**, **host** nebo  **public**.
admin-password | *bezpečné heslo* | Heslo uživatele, který je správcem. Musí mít 8 až 128 znaků. Heslo musí obsahovat znaky ze tří z následujících kategorií: Velká písmena anglické abecedy, malá písmena, čísla a jiné než alfanumerické znaky.


Hodnota parametru sku-name má formát {cenová_úroveň}\_{výpočetní_generace}\_{počet_virtuálních_jader} jako v následujících příkladech:
+ `--sku-name B_Gen4_4` se mapuje na úroveň Basic 4. generace se 4 virtuálními jádry.
+ `--sku-name GP_Gen5_32` se mapuje na úroveň pro obecné účely 5. generace se 32 virtuálními jádry.
+ `--sku-name MO_Gen5_2` se mapuje na úroveň optimalizovanou pro paměť 5. generace se 2 virtuálními jádry.

Vysvětlení platných hodnot pro jednotlivé oblasti a úrovně najdete v dokumentaci k [cenovým úrovním](./concepts-pricing-tiers.md).

Následující příklad vytvoří server MySQL 5.7 v umístění USA – západ, `mydemoserver` ve vaší skupině prostředků `myresourcegroup` a s přihlašovacím jménem správce serveru `myadmin`. Jedná se o **Univerzální** server **4. generace** se 2 **virtuálními jádry**. Nahraďte položku `<server_admin_password>` vlastní hodnotou.
```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```




## <a name="configure-firewall-rule"></a>Konfigurace pravidla brány firewall
Vytvořte pravidlo brány firewall na úrovni serveru pro Azure Database for MySQL pomocí příkazu **[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create)**. Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci, jako je například nástroj příkazového řádku **mysql.exe** nebo MySQL Workbench, aby se k vašemu serveru připojila prostřednictvím brány firewall služby Azure MySQL. 

Následující příklad vytvoří pravidlo brány firewall `AllowMyIP`, které povolí připojení z konkrétní IP adresy 192.168.0.1. Nahraďte ji za IP adresu nebo rozsah IP adres, které odpovídají umístění, ze kterého se budete připojovat. 

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

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
Připojte se k serveru pomocí nástroje příkazového řádku **mysql.exe**. MySQL můžete stáhnout [odsud](https://dev.mysql.com/downloads/) a nainstalovat do svého počítače. 

Zadejte další příkazy: 

1. Připojení k serveru pomocí nástroje příkazového řádku **mysql**:
```bash
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

Pokud chcete odstranit jenom nově vytvořený server, můžete spustit příkaz **[az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete)**.
```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Návrh databáze MySQL pomocí rozhraní příkazového řádku Azure](./tutorial-design-database-using-cli.md)
