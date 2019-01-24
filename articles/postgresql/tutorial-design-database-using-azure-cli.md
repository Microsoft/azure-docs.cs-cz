---
title: 'Kurz: Návrh databáze Azure Database for PostgreSQL pomocí Azure CLI'
description: Tento kurz ukazuje, jak vytvořit, nakonfigurovat a dotazovat první server Azure Database for PostgreSQL pomocí Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 04/01/2018
ms.openlocfilehash: 1782aa2a29bc2cf2593aa5bb1c356d4176196055
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54854407"
---
# <a name="tutorial-design-an-azure-database-for-postgresql-using-azure-cli"></a>Kurz: Návrh databáze Azure Database for PostgreSQL pomocí Azure CLI 
V tomto kurzu použijete Azure CLI (rozhraní příkazového řádku) a další nástroje k získání informací o těchto tématech:
> [!div class="checklist"]
> * Vytvoření serveru Azure Database for PostgreSQL
> * Konfigurace brány firewall serveru
> * Vytvoření databáze pomocí nástroje [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html)
> * Načtení ukázkových dat
> * Dotazování dat
> * Aktualizace dat
> * Obnovení dat

Ke spuštění příkazů v tomto kurzu můžete použít Azure Cloud Shell v prohlížeči nebo si [nainstalujte Azure CLI]( /cli/azure/install-azure-cli) ve vašem počítači.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém tento prostředek existuje nebo ve kterém se fakturuje. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account#az_account_set) určité ID předplatného.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte [skupinu prostředků Azure](../azure-resource-manager/resource-group-overview.md) pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina. Následující příklad vytvoří skupinu prostředků s názvem `myresourcegroup` v umístění `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Vytvoření serveru Azure Database for PostgreSQL
Vytvořte [server Azure Database for PostgreSQL](overview.md) pomocí příkazu [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create). Server obsahuje soubor databází spravovaných jako skupina. 

Následující příklad vytvoří ve skupině prostředků `myresourcegroup` server s názvem `mydemoserver` a přihlašovacím jménem správce serveru `myadmin`. Název serveru se mapuje na název DNS, a proto musí být v rámci Azure globálně jedinečný. Nahraďte položku `<server_admin_password>` vlastní hodnotou. Jedná se o server pro obecné účely Gen 4 se 2 virtuálními jádry.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 9.6
```
Hodnota parametru sku-name má formát {cenová_úroveň}\_{výpočetní_generace}\_{počet_virtuálních_jader} jako v následujících příkladech:
+ `--sku-name B_Gen4_4` se mapuje na úroveň Basic 4. generace se 4 virtuálními jádry.
+ `--sku-name GP_Gen5_32` se mapuje na úroveň pro obecné účely 5. generace se 32 virtuálními jádry.
+ `--sku-name MO_Gen5_2` se mapuje na úroveň optimalizovanou pro paměť 5. generace se 2 virtuálními jádry.

Vysvětlení platných hodnot pro jednotlivé oblasti a úrovně najdete v dokumentaci k [cenovým úrovním](./concepts-pricing-tiers.md).

> [!IMPORTANT]
> Zde zadané přihlašovací jméno a heslo správce serveru se vyžadují pro přihlášení k serveru a jeho databázím dále v tomto rychlém startu. Tyto informace si zapamatujte nebo poznamenejte pro pozdější použití.

Ve výchozím nastavení se databáze **postgres** vytvoří v rámci vašeho serveru. Databáze [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) je výchozí databáze určená pro uživatele, nástroje a aplikace třetích stran. 


## <a name="configure-a-server-level-firewall-rule"></a>Konfigurace pravidla brány firewall na úrovni serveru

Pomocí příkazu [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) vytvořte pravidlo brány firewall na úrovni serveru Azure PostgreSQL. Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci, jako je třeba [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) nebo [PgAdmin](https://www.pgadmin.org/), aby se k vašemu serveru připojila prostřednictvím brány firewall služby Azure PostgreSQL. 

Abyste se mohli připojit z vaší sítě, můžete nastavit pravidlo brány firewall, které pokrývá rozsah IP adres. Následující příklad vytvoří pomocí příkazu [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) pravidlo brány firewall `AllowMyIP` umožňující připojení z jedné IP adresy.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

Pokud chcete přístup k serveru Azure PostgreSQL omezit pouze na vaši síť, můžete v pravidlu brány firewall nastavit pokrytí pouze rozsahu IP adres vaší podnikové sítě.

> [!NOTE]
> Server Azure PostgreSQL komunikuje přes port 5432. Pokud se připojujete z podnikové sítě, nemusí být odchozí provoz přes port 5432 bránou firewall vaší sítě povolený. Pořádejte oddělení IT, aby otevřeli port 5432 pro připojení k vašemu serveru Azure SQL Database.
>

## <a name="get-the-connection-information"></a>Získání informací o připojení

Pokud se chcete připojit k serveru, budete muset zadat informace o hostiteli a přihlašovací údaje pro přístup.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Výsledek je ve formátu JSON. Poznamenejte si položky **administratorLogin** a **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"

}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Připojení k databázi Azure Database for PostgreSQL pomocí nástroje psql
Pokud má klientský počítač nainstalovaný systém PostgreSQL, můžete se připojit k serveru Azure PostgreSQL pomocí místní instance nástroje [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) nebo konzoly Azure Cloud Shell. Teď pro připojení k serveru Azure Database for PostgreSQL použijeme nástroj příkazového řádku psql.

1. Spuštěním následujícího příkazu psql se připojte k databázi Azure Database for PostgreSQL:
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Třeba tento příkaz provádí pomocí přihlašovacích údajů pro přístup připojení k výchozí databázi s názvem **postgres** na serveru PostgreSQL **mydemoserver.postgres.database.azure.com**. Zadejte heslo `<server_admin_password>`, které jste zvolili při zobrazení výzvy k zadání hesla.
  
  ```azurecli-interactive
psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
```

2.  Po připojení k serveru vytvořte na příkazovém řádku prázdnou databázi:
```sql
CREATE DATABASE mypgsqldb;
```

3.  Na příkazovém řádku spusťte následující příkaz, který přepne připojení na nově vytvořenou databázi **mypgsqldb**:
```sql
\c mypgsqldb
```

## <a name="create-tables-in-the-database"></a>Vytvoření tabulek v databázi
Teď víte, jak se připojit k databázi Azure Database for PostgreSQL, a můžete začít provádět některé základní úlohy:

Nejdřív vytvoříte tabulku a načtete do ní data. Vytvořte například tabulku, která sleduje informace o inventáři:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Teď můžete nově vytvořenou databázi zobrazit v seznamu databází zadáním:
```sql
\dt
```

## <a name="load-data-into-the-table"></a>Načtení dat do tabulky
Když teď máte vytvořenou tabulku, přidejte do ní nějaká data. V otevřeném okně příkazového řádku spusťte následující dotaz, který vloží několik řádků dat:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Právě jste do tabulky, kterou jste vytvořili dříve, vložili dva řádky ukázkových dat.

## <a name="query-and-update-the-data-in-the-tables"></a>Dotazování na data a aktualizace dat v tabulkách
Provedením následujícího dotazu načtěte informace z tabulky inventáře: 
```sql
SELECT * FROM inventory;
```

Data v tabulce inventáře můžete také aktualizovat:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Aktualizované hodnoty se zobrazí po načtení dat:
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Obnovení databáze k dřívějšímu bodu v čase
Představte si, že jste některou tabulku omylem odstranili. Taková situace se těžko napravuje. Azure Database for PostgreSQL umožňuje přejít zpět k jakémukoli bodu v čase, pro který má server zálohy (závisí na konfigurovaném období uchovávání záloh), a obnovit tento bod v čase na nový server. Tento nový server můžete použít k obnovení odstraněných dat. 

Následující příklad obnoví ukázkový server do bodu před přidáním tabulky:
```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mydemoserver
```

Příkaz `az postgres server restore` potřebuje následující parametry:
| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, ve které se nachází zdrojový server.  |
| jméno | mydemoserver-restored | Název nového serveru, který se vytvoří příkazem restore. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Vyberte bod v čase, ke kterému se má provést obnovení. Tato datum a čas musí být v rámci doby uchovávání záloh zdrojového serveru. Použijte formát data a času ISO8601. Můžete použít například své místní časové pásmo, třeba `2017-04-13T05:59:00-08:00`, nebo formát UTC Zulu `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | Název nebo ID zdrojového serveru, ze kterého se má provést obnovení. |

Obnovení serveru k bodu v čase vytvoří nový server jako kopii původního serveru k zadanému bodu v čase. Umístění a cenová úroveň obnoveného serveru jsou stejné jako u zdrojového serveru.

Příkaz je synchronní a vrátí se po obnovení serveru. Po dokončení obnovení vyhledejte nově vytvořený server. Ověřte, že se data obnovila podle očekávání.


## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak pomocí Azure CLI (rozhraní příkazového řádku) a dalších nástrojů provést následující:
> [!div class="checklist"]
> * Vytvoření serveru Azure Database for PostgreSQL
> * Konfigurace brány firewall serveru
> * Vytvoření databáze pomocí nástroje [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html)
> * Načtení ukázkových dat
> * Dotazování dat
> * Aktualizace dat
> * Obnovení dat

V dalším kroku zjistěte, jak podobné úlohy provést, přečtěte si tento kurz pomocí webu Azure portal: [Návrh první databáze Azure Database for PostgreSQL pomocí webu Azure portal](tutorial-design-database-using-azure-portal.md)
