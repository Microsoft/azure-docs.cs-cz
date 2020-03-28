---
title: 'Kurz: Vytvoření azure databáze pro MySQL – šablona Azure Resource Manager'
description: Tento kurz vysvětluje, jak zřídit a automatizovat nasazení Azure Database for MySQL server pomocí šablony Azure Resource Manager.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/02/2019
ms.custom: mvc
ms.openlocfilehash: f4960482c88bf9768be1c1c9dbb3652409a8f1b8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74771084"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Kurz: Zřízení databáze Azure pro server MySQL pomocí šablony Azure Resource Manager

Rozhraní [Azure Database for MySQL REST API](https://docs.microsoft.com/rest/api/mysql/) umožňuje technikům DevOps automatizovat a integrovat zřizování, konfiguraci a provoz spravovaných serverů MySQL a databází v Azure.  Rozhraní API umožňuje vytváření, výčet, správu a odstranění serverů mysql a databází ve službě Azure Database for MySQL.

Azure Resource Manager využít základní rozhraní REST API deklarovat a program ovat prostředky Azure potřebné pro nasazení ve velkém měřítku, zarovnání s infrastrukturou jako koncept kódu. Šablona parametrizuje název prostředku Azure, skladovou položku, síť, konfiguraci brány firewall a nastavení, což umožňuje, aby byl vytvořen jednou a použit vícekrát.  Šablony Azure Resource Manageru lze snadno vytvořit pomocí [portálu Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal) nebo [kódu Visual Studia](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code?tabs=CLI). Umožňují balení aplikací, standardizaci a automatizaci nasazení, které lze integrovat do kanálu DevOps CI/CD.  Například pokud hledáte rychlé nasazení webové aplikace s azure database pro mysql back-end, můžete provést nasazení začátku do konce pomocí této [šablony QuickStart](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/) z galerie GitHub.

V tomto kurzu pomocí šablony Azure Resource Manager a další nástroje se dozvíte, jak:

> [!div class="checklist"]
> * Vytvoření databáze Azure pro server MySQL pomocí koncového bodu služby VNet pomocí šablony Azure Resource Manager
> * Vytvoření databáze pomocí [nástroje příkazového řádku mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)
> * Načtení ukázkových dat
> * Dotazování dat
> * Aktualizace dat

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Vytvoření databáze Azure pro server MySQL pomocí koncového bodu služby VNet pomocí šablony Azure Resource Manager

Chcete-li získat odkaz na šablonu JSON pro azure databázi pro server MySQL, přejděte na odkaz na šablonu [serverů Microsoft.DBforMySQL.](/azure/templates/microsoft.dbformysql/servers) Níže je ukázka šablony JSON, kterou lze použít k vytvoření nového serveru se systémem Azure Database for MySQL s koncovým bodem služby Virtuální sítě.
```json
{
  "apiVersion": "2017-12-01",
  "type": "Microsoft.DBforMySQL/servers",
  "name": "string",
  "location": "string",
  "tags": "string",
  "properties": {
    "version": "string",
    "sslEnforcement": "string",
    "administratorLogin": "string",
    "administratorLoginPassword": "string",
    "storageProfile": {
      "storageMB": "string",
      "backupRetentionDays": "string",
      "geoRedundantBackup": "string"
    }
  },
  "sku": {
    "name": "string",
    "tier": "string",
    "capacity": "string",
    "family": "string"
  },
  "resources": [
    {
      "name": "AllowSubnet",
      "type": "virtualNetworkRules",
      "apiVersion": "2017-12-01",
      "properties": {
        "virtualNetworkSubnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "ignoreMissingVnetServiceEndpoint": true
      },
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/servers/', parameters('serverName'))]"
      ]
    }
  ]
}
```
V tomto požadavku jsou hodnoty, které je třeba přizpůsobit, tyto hodnoty:
+   `name`- Zadejte název vašeho MySQL Serveru (bez názvu domény).
+   `location`- Zadejte platnou oblast datového centra Azure pro váš MySQL Server. Například westus2.
+   `properties/version`- Zadejte verzi serveru MySQL k nasazení. Například 5,6 nebo 5,7.
+   `properties/administratorLogin`- Zadejte MySQL admin přihlášení pro server. Přihlašovací jméno správce nemůže být azure_superuser, admin, administrator, root, guest ani public.
+   `properties/administratorLoginPassword`- Zadejte heslo pro uživatele správce MySQL uvedené výše.
+   `properties/sslEnforcement`- Zadejte povoleno/zakázáno povolit / zakázat sslEnforcement.
+   `storageProfile/storageMB`- Zadejte maximální velikost zřízeného úložiště požadovanou pro server v megabajtech. Například 5120.
+   `storageProfile/backupRetentionDays`- Zadejte požadovanou dobu uchování zálohy ve dnech. Například 7. 
+   `storageProfile/geoRedundantBackup`- Zadejte povoleno / zakázáno v závislosti na požadavcích geo-DR.
+   `sku/tier`- Zadejte základní, GeneralPurpose nebo MemoryOptimized vrstvy pro nasazení.
+   `sku/capacity`- Zadejte kapacitu virtuálních jader. Možné hodnoty zahrnují 2, 4, 8, 16, 32 nebo 64.
+   `sku/family`- Zadejte Gen5 zvolit generování hardwaru pro nasazení serveru.
+   `sku/name`- Uveďte TierPrefix_family_capacity. Například B_Gen5_1, GP_Gen5_16, MO_Gen5_32. Informace o [platných hodnotách](./concepts-pricing-tiers.md) podle oblasti a na úroveň najdete v dokumentaci k cenovým úrovním.
+   `resources/properties/virtualNetworkSubnetId`– Zadejte identifikátor Azure podsítě ve virtuální síti, kde by měl být umístěn server Azure MySQL. 
+   `tags(optional)`- Zadejte volitelné značky jsou klíčové hodnoty párů, které byste použít ke kategorizaci zdrojů pro fakturaci atd.

Pokud chcete vytvořit šablonu Azure Resource Manager pro automatizaci azure database pro nasazení MySQL pro vaši organizaci, doporučujeme začít z ukázkové [šablony Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) v Azure Quickstart GitHub Gallery a stavět na ní. 

Pokud se šablonami Azure Resource Manageru tečujete a chcete je vyzkoušet, můžete začít takto:
+   Klonujte nebo stáhněte ukázkovou [šablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) u galerie Azure Quickstart.  
+   Upravte soubor azuredeploy.parameters.json a aktualizujte hodnoty parametrů na základě vašich preferencí a uložte soubor. 
+   Vytvoření serveru Azure MySQL pomocí následujících příkazů pomocí azure cli

Ke spuštění bloků kódu v tomto kurzu můžete použít Azure Cloud Shell v prohlížeči nebo si nainstalujte Azure CLI ve vašem počítači.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l "West US2"
az group deployment create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
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
  "location": "westus2",
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

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:
> [!div class="checklist"]
> * Vytvoření databáze Azure pro server MySQL pomocí koncového bodu služby VNet pomocí šablony Azure Resource Manager
> * Vytvoření databáze pomocí [nástroje příkazového řádku mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)
> * Načtení ukázkových dat
> * Dotazování dat
> * Aktualizace dat
> 
> [Postup připojení aplikací k Azure Database for MySQL](./howto-connection-string.md)
