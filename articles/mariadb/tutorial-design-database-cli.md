---
title: 'Kurz: Návrh databáze Azure pro MariaDB pomocí Azure CLI'
description: Tento kurz vysvětluje, jak vytvářet a spravovat Azure Database pro MariaDB server a databáze pomocí rozhraní příkazového řádku Azure z příkazového řádku.
services: mariadb
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 11/10/2018
ms.custom: mvc
ms.openlocfilehash: 2d715f89b56af822c2c1174cca0f2a9c1b847fc0
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516329"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-azure-cli"></a>Kurz: Návrh databáze Azure pro MariaDB pomocí Azure CLI

Azure Database pro MariaDB je služba relačních databází v cloudu Microsoftu založená na databázovém stroji MariaDB Community Edition. V tomto kurzu použijete Azure CLI (rozhraní příkazového řádku) a další nástroje k získání informací o těchto tématech:

> [!div class="checklist"]
> * Vytvoření databáze Azure Database for MariaDB
> * Konfigurace brány firewall serveru
> * Použití [nástroje pro příkazový řádek mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) k vytvoření databáze
> * Načtení ukázkových dat
> * Dotazování dat
> * Aktualizace dat
> * Obnovení dat

Ke spuštění bloků kódu v tomto kurzu můžete použít Azure Cloud Shell v prohlížeči nebo si [nainstalujte Azure CLI]( /cli/azure/install-azure-cli) ve vašem počítači.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém tento prostředek existuje nebo ve kterém se fakturuje. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account#az-account-set) určité ID předplatného.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte [skupinu prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina.

Následující příklad vytvoří skupinu prostředků s názvem `myresourcegroup` v umístění `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Vytvoření serveru Azure Database for MariaDB
Vytvoření serveru Azure Database for MariaDB s `az mariadb server create` příkazu. Server může spravovat více databází. Obvykle se pro jednotlivé projekty nebo uživatele používají samostatné databáze.

Následující příklad vytvoří Azure Database pro MariaDB server umístěný v `westus` ve skupině prostředků `myresourcegroup` s názvem `mydemoserver`. Server má správce s přihlašovacím jménem `myadmin`. Je pro obecné účely, generace 5 serveru se 2 virtuálními jádry. Nahraďte položku `<server_admin_password>` vlastní hodnotou.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```
Hodnota parametru sku-name má formát {cenová_úroveň}\_{výpočetní_generace}\_{počet_virtuálních_jader} jako v následujících příkladech:
+ `--sku-name B_Gen5_4` se mapuje na úroveň Basic 5. generace se 4 virtuálními jádry.
+ `--sku-name GP_Gen5_32` se mapuje na úroveň pro obecné účely 5. generace se 32 virtuálními jádry.
+ `--sku-name MO_Gen5_2` se mapuje na úroveň optimalizovanou pro paměť 5. generace se 2 virtuálními jádry.

Vysvětlení platných hodnot pro jednotlivé oblasti a úrovně najdete v dokumentaci k [cenovým úrovním](./concepts-pricing-tiers.md).

> [!IMPORTANT]
> Zde zadané přihlašovací jméno a heslo správce serveru se vyžadují pro přihlášení k serveru a jeho databázím dále v tomto rychlém startu. Tyto informace si zapamatujte nebo poznamenejte pro pozdější použití.


## <a name="configure-firewall-rule"></a>Konfigurace pravidla brány firewall
Vytvoření Azure Database pro MariaDB pravidlo brány firewall na úrovni serveru s `az mariadb server firewall-rule create` příkazu. Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci, jako například **mysql** nástroj příkazového řádku nebo MySQL Workbench, aby připojení k serveru přes bránu firewall služby Azure MariaDB.

Následující příklad vytvoří pravidlo brány firewall `AllowMyIP`, které povolí připojení z konkrétní IP adresy 192.168.0.1. Nahraďte ji za IP adresu nebo rozsah IP adres, které odpovídají umístění, ze kterého se budete připojovat.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
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
  "administratorLoginPassword": null,
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

## <a name="connect-to-the-server-using-mysql"></a>Připojení k serveru pomocí mysql
Pomocí [nástroje pro příkazový řádek mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) navažte připojení k serveru Azure Database for MariaDB. V tomto příkladu se použije příkaz:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Vytvoření prázdné databáze
Po připojení k serveru vytvořte prázdnou databázi.
```sql
mysql> CREATE DATABASE mysampledb;
```

Na příkazovém řádku spusťte následující příkaz pro přepnutí připojení na tuto nově vytvořenou databázi:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Vytvoření tabulek v databázi
Teď, když víte, jak se připojit ke službě Azure Database pro MariaDB databáze, dokončíte některé základní úlohy.

Nejdřív vytvoříte tabulku a načtete do ní data. Pojďme vytvořit tabulku, ve které jsou uložené informace o inventáři.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Načtení dat do tabulek
Teď máte vytvořenou tabulku a můžete do ní vložit data. V otevřeném okně příkazového řádku spusťte následující dotaz, který vloží několik řádků dat.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Tabulka, kterou jste vytvořili dříve, teď obsahuje dva řádky ukázkových dat.

## <a name="query-and-update-the-data-in-the-tables"></a>Dotazování na data a aktualizace dat v tabulkách
Spuštěním následujícího příkazu načtete informace z databázové tabulky.
```sql
SELECT * FROM inventory;
```

Data v tabulce můžete také aktualizovat.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Při načtení dat se řádek příslušným způsobem aktualizuje.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Obnovení databáze k dřívějšímu bodu v čase
Představte si, že jste tuto tabulku omylem odstranili. Taková situace se těžko napravuje. Azure Database pro MariaDB umožňuje vrátit do libovolného bodu v čase během posledních až 35 dnů a obnovit tento bod v čase na nový server. Tento nový server můžete použít k obnovení odstraněných dat. Následující kroky obnoví ukázkový server do bodu před přidáním tabulky.

K obnovení budete potřebovat následující informace:

- Bod obnovení: Vyberte bod v čase, který nastal dřív, než došlo ke změně serveru. Musí být větší nebo rovný hodnotě Nejstarší záloha zdrojové databáze.
- Cílový server: Zadejte nový název serveru, na který chcete provést obnovení.
- Zdrojový server: Zadejte název serveru, ze kterého se má obnovení provést.
- Umístění: Nejde vyberte oblast, ve výchozím nastavení je stejná jako u zdrojového serveru.

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

Příkaz `az mariadb server restore` potřebuje následující parametry:
| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, ve které se nachází zdrojový server.  |
| jméno | mydemoserver-restored | Název nového serveru, který se vytvoří příkazem restore. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Vyberte bod v čase, ke kterému se má provést obnovení. Tato datum a čas musí být v rámci doby uchovávání záloh zdrojového serveru. Použijte formát data a času ISO8601. Můžete použít například své místní časové pásmo, třeba `2017-04-13T05:59:00-08:00`, nebo formát UTC Zulu `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | Název nebo ID zdrojového serveru, ze kterého se má provést obnovení. |

Obnovení serveru k bodu v čase vytvoří nový server jako kopii původního serveru k zadanému bodu v čase. Umístění a cenová úroveň obnoveného serveru jsou stejné jako u zdrojového serveru.

Příkaz je synchronní a vrátí se po obnovení serveru. Po dokončení obnovení vyhledejte nově vytvořený server. Ověřte, že se data obnovila podle očekávání.

## <a name="next-steps"></a>Další postup
V tomto kurzu jste se naučili:
> [!div class="checklist"]
> * Vytvoření serveru Azure Database for MariaDB
> * Konfigurace brány firewall serveru
> * Použití [nástroje pro příkazový řádek mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) k vytvoření databáze
> * Načtení ukázkových dat
> * Dotazování dat
> * Aktualizace dat
> * Obnovení dat
