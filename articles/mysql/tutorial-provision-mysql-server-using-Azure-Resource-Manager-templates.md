---
title: 'Kurz: Zřízení serveru Azure Database for MySQL pomocí šablony Azure Resource Manageru'
description: Tento kurz vysvětluje, jak zřídit a automatizovat – Azure Database for MySQL server nasazení pomocí šablony Azure Resource Manageru.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/21/2018
ms.custom: mvc
ms.openlocfilehash: 3c89c5cc0b299852f85836dd416b5bb270757719
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54061036"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Kurz: Zřízení serveru Azure Database for MySQL pomocí šablony Azure Resource Manageru

[– Azure Database for MySQL – REST API](https://docs.microsoft.com/en-us/rest/api/mysql/) umožňuje technikům DevOps pro automatizaci a integraci zřizování, konfigurace a operace objektu spravovaný MySQL servery a databáze v Azure.  Rozhraní API umožňuje vytváření, výčet, Správa a odstranění databáze a servery MySQL na služba Azure Database for MySQL.

Základní rozhraní REST API pro deklaraci a program prostředky Azure potřebné pro nasazení ve velkém měřítku, zarovnání infrastruktury jako kódu konceptu využít Azure Resource Manageru. Šablona parametrizuje sítě název, skladovou Položku, prostředků Azure, konfigurace brány firewall a nastavení, což umožňuje vytvořit jeden čas a použít více než jednou.  Šablony Azure Resource Manageru můžete snadno vytvořit pomocí [webu Azure portal](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal) nebo [Visual Studio Code](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code?tabs=CLI). Umožňují balení aplikace pro normalizaci a automatizaci nasazení, které je možné integrovat se v kanálu DevOps CI/CD.  Například pokud chcete rychle nasadit webové aplikace s využitím Azure Database for MySQL back-endu, můžete provést pomocí tohoto nasazení začátku do konce [šablonu pro rychlý Start](https://azure.microsoft.com/en-us/resources/templates/101-webapp-managed-mysql/) v galerii Githubu.

V tomto kurzu jste pomocí šablony Azure Resource Manageru a další nástroje pro další postup:

> [!div class="checklist"]
> * Vytvoření serveru Azure Database for MySQL pomocí koncového bodu služby virtuální sítě pomocí šablony Azure Resource Manageru
> * Použití [nástroje pro příkazový řádek mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) k vytvoření databáze
> * Načtení ukázkových dat
> * Dotazování dat
> * Aktualizace dat

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Vytvoření serveru Azure Database for MySQL pomocí koncového bodu služby virtuální sítě pomocí šablony Azure Resource Manageru

Chcete-li získat odkaz na šablonu JSON na serveru Azure Database for MySQL, přejděte na [Microsoft.DBforMySQL servery](/azure/templates/microsoft.dbformysql/servers) referenčními informacemi k šablonám. Níže je ukázkové šablony JSON, který slouží k vytvoření nového serveru se systémem – Azure Database for MySQL pomocí koncového bodu služby virtuální sítě.
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
V této žádosti, které je potřeba upravit hodnoty jsou:
+   `name` -Zadejte název vašeho serveru MySQL (bez názvu domény).
+   `location` -Zadejte platná data Azure středová oblast pro váš MySQL Server. Například westus2.
+   `properties/version` -Zadejte verzi serveru MySQL pro nasazení. Například 5.6 nebo 5.7.
+   `properties/administratorLogin` -Zadejte přihlašovací jméno správce MySQL serveru. Přihlašovací jméno správce nemůže být azure_superuser, správce, správce, root, guest nebo veřejné.
+   `properties/administratorLoginPassword` -Zadejte heslo pro uživatele s rolí správce MySQL výše uvedené.
+   `properties/sslEnforcement` -Zadejte povolení nebo zakázání, povolení/zakázání sslEnforcement.
+   `storageProfile/storageMB` -Zadejte maximální velikost úložiště zřízená velikost požadované pro server v megabajtech. Například 5120.
+   `storageProfile/backupRetentionDays` -Zadejte období požadované uchování zálohy ve dnech. Například 7. 
+   `storageProfile/geoRedundantBackup` -Zadejte povolení nebo zakázání v závislosti na požadavcích Geo-DR.
+   `sku/tier` -Zadejte úroveň Basic, GeneralPurpose nebo MemoryOptimized pro nasazení.
+   `sku/capacity` -Zadejte kapacity vCore. Možné hodnoty zahrnují 2, 4, 8, 16, 32 nebo 64.
+   `sku/family` -Zadejte Gen4 nebo Gen5 zvolte generace hardwaru pro nasazení serveru.
+   `sku/name` -Zadejte TierPrefix_family_capacity. Například B_Gen4_1 GP_Gen5_16, MO_Gen5_32. Zobrazit [cenové úrovně](./concepts-pricing-tiers.md) dokumentaci o platné hodnoty v jedné oblasti a na úroveň.
+   `resources/properties/virtualNetworkSubnetId` -Zadejte identifikátor Azure v podsíti ve virtuální síti, kam umístit server Azure MySQL. 
+   `tags(optional)` -Zadat volitelný značky jsou páry klíč-hodnota, které můžete využít ke kategorizaci prostředky pro fakturaci atd.

Pokud chcete vytvořit šablonu Azure Resource Manageru pro automatizaci – Azure Database for MySQL nasazení pro vaši organizaci, doporučení by spuštění z ukázky [šablony Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) v Azure Rychlý start Githubu Galerie první a sestavení dojde k jeho zvýraznění. 

Pokud začínáte se šablonami Azure Resource Manageru a chcete vyzkoušet, můžete spustit pomocí následujících kroků:
+   Klonovat nebo stáhnout ukázku [šablony Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) z Galerie Azure pro rychlý start.  
+   Upravit azuredeploy.parameters.json aktualizovat hodnoty parametrů, které jsou založené na vašich předvoleb a soubor uložte. 
+   Pomocí Azure CLI k vytvoření serveru Azure MySQL pomocí následujících příkazů

Azure Cloud Shell v prohlížeči nebo instalace Azure CLI můžete ve vašem počítači použít ke spuštění bloků kódu v tomto kurzu.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l “West US2”
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

## <a name="next-steps"></a>Další postup
V tomto kurzu jste se naučili:
> [!div class="checklist"]
> * Vytvoření serveru Azure Database for MySQL pomocí koncového bodu služby virtuální sítě pomocí šablony Azure Resource Manageru
> * Použití [nástroje pro příkazový řádek mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) k vytvoření databáze
> * Načtení ukázkových dat
> * Dotazování dat
> * Aktualizace dat

> [Postup připojení aplikací k Azure Database for MySQL](./howto-connection-string.md)
