---
title: "Návrh první databáze Azure Database for MySQL – Azure CLI"
description: "Tento kurz vysvětluje, jak vytvořit a spravovat databázi a server Azure Database for MySQL pomocí Azure CLI 2.0 z příkazového řádku."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 02/28/2018
ms.custom: mvc
ms.openlocfilehash: 779e6b48a20dd49967a189293ed37b07bc5e1cda
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
---
# <a name="design-your-first-azure-database-for-mysql-database"></a>Návrh první databáze Azure Database for MySQL

Azure Database for MySQL je relační databázová služba v cloudu Microsoftu založená na databázovém stroji MySQL Community Edition. V tomto kurzu použijete Azure CLI (rozhraní příkazového řádku) a další nástroje k získání informací o těchto tématech:

> [!div class="checklist"]
> * Vytvoření Azure Database for MySQL
> * Konfigurace brány firewall serveru
> * Použití [nástroje pro příkazový řádek mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) k vytvoření databáze
> * Načtení ukázkových dat
> * Dotazování dat
> * Aktualizace dat
> * Obnovení dat

Ke spuštění bloků kódu v tomto kurzu můžete použít Azure Cloud Shell v prohlížeči nebo si [nainstalujte Azure CLI 2.0]( /cli/azure/install-azure-cli) ve vašem počítači.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku (CLI) místně, musíte mít spuštěnou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Pokud máte více předplatných, vyberte odpovídající předplatné, ve kterém tento prostředek existuje nebo ve kterém se fakturuje. Ve svém účtu vyberte pomocí příkazu [az account set](/cli/azure/account#az_account_set) určité ID předplatného.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte [skupinu prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina.

Následující příklad vytvoří skupinu prostředků s názvem `myresourcegroup` v umístění `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
## <a name="add-the-extension"></a>Přidání rozšíření
Přidejte aktualizované rozšíření pro správu služby Azure Database for MySQL pomocí následujícího příkazu:
```azurecli-interactive
az extension add --name rdbms
``` 
## <a name="create-an-azure-database-for-mysql-server"></a>Vytvoření serveru Azure Database for MySQL
Vytvořte server Azure Database for MySQL pomocí příkazu az mysql server create. Server může spravovat více databází. Obvykle se pro jednotlivé projekty nebo uživatele používají samostatné databáze.

Následující příklad vytvoří server Azure Database for MySQL, jehož umístěním je `westus` ve skupině prostředků `myresourcegroup` s názvem `mydemoserver`. Server má správce s přihlašovacím jménem `myadmin`. Jedná se o server pro obecné účely Gen 4 se 2 virtuálními jádry. Nahraďte položku `<server_admin_password>` vlastní hodnotou.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```
> [!IMPORTANT]
> Zde zadané přihlašovací jméno a heslo správce serveru se vyžadují pro přihlášení k serveru a jeho databázím dále v tomto rychlém startu. Tyto informace si zapamatujte nebo poznamenejte pro pozdější použití.


## <a name="configure-firewall-rule"></a>Konfigurace pravidla brány firewall
Pomocí příkazu az mysql server firewall-rule create vytvořte pravidlo brány firewall na úrovni serveru pro Azure Database for MySQL. Pravidlo brány firewall na úrovni serveru umožňuje externí aplikaci, jako je například nástroj pro příkazový řádek **mysql** nebo MySQL Workbench, aby se k vašemu serveru připojila prostřednictvím brány firewall služby Azure MySQL. 

Následující příklad vytvoří pravidlo brány firewall pro rozsah předdefinovaných adres. Tento příklad ukazuje celý možný rozsah IP adres.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowAllIPs --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
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
  "administratorLoginPassword": null,
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

## <a name="connect-to-the-server-using-mysql"></a>Připojení k serveru pomocí mysql
Pro navázání připojení k serveru Azure Database for MySQL použijte [nástroj pro příkazový řádek mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html). V tomto příkladu se použije příkaz:
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
Teď, když už víte, jak se připojit k databázi Azure Database for MySQL, dokončíte některé základní úlohy.

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
Představte si, že jste tuto tabulku omylem odstranili. Z této situace se nejde snadno zotavit. Azure Database for MySQL umožňuje vrátit se do libovolného bodu v čase během posledních až 35 dnů a obnovit tento bod v čase na nový server. Tento nový server můžete použít k obnovení odstraněných dat. Následující kroky obnoví ukázkový server do bodu před přidáním tabulky.

K obnovení budete potřebovat následující informace:

- Bod obnovení: Vyberte bod v čase, který nastal dřív, než došlo ke změně serveru. Musí být větší nebo rovný hodnotě Nejstarší záloha zdrojové databáze.
- Cílový server: Zadejte nový název serveru, na který chcete provést obnovení.
- Zdrojový server: Zadejte název serveru, ze kterého se má obnovení provést.
- Umístění: Nejde vyberte oblast, ve výchozím nastavení je stejná jako u zdrojového serveru.

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

Příkaz `az mysql server restore` potřebuje následující parametry:
| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Skupina prostředků, ve které se nachází zdrojový server.  |
| jméno | mydemoserver-restored | Název nového serveru, který se vytvoří příkazem restore. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Vyberte bod v čase, ke kterému se má provést obnovení. Tato datum a čas musí být v rámci doby uchovávání záloh zdrojového serveru. Použijte formát data a času ISO8601. Můžete použít například své místní časové pásmo, třeba `2017-04-13T05:59:00-08:00`, nebo formát UTC Zulu `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | Název nebo ID zdrojového serveru, ze kterého se má provést obnovení. |

Obnovení serveru k bodu v čase vytvoří nový server jako kopii původního serveru k zadanému bodu v čase. Umístění a cenová úroveň obnoveného serveru jsou stejné jako u zdrojového serveru.

Příkaz je synchronní a vrátí se po obnovení serveru. Po dokončení obnovení vyhledejte nově vytvořený server. Ověřte, že se data obnovila podle očekávání.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:
> [!div class="checklist"]
> * Vytvoření serveru Azure Database for MySQL
> * Konfigurace brány firewall serveru
> * Použití [nástroje pro příkazový řádek mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) k vytvoření databáze
> * Načtení ukázkových dat
> * Dotazování dat
> * Aktualizace dat
> * Obnovení dat

> [!div class="nextstepaction"]
> [Azure Database for MySQL – ukázky v Azure CLI](./sample-scripts-azure-cli.md)
