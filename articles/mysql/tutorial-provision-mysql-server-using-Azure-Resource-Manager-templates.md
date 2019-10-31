---
title: 'Kurz: zřízení Azure Database for MySQL serveru pomocí šablony Azure Resource Manager'
description: V tomto kurzu se dozvíte, jak zřídit a automatizovat Azure Database for MySQL nasazení na serverech pomocí Azure Resource Manager šablony.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/21/2018
ms.custom: mvc
ms.openlocfilehash: 69025dd70ffe88c1592cf656e956b3e78a97a5e7
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163903"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Kurz: zřízení Azure Database for MySQL serveru pomocí šablony Azure Resource Manager

[Azure Database for MySQL REST API](https://docs.microsoft.com/rest/api/mysql/) umožňuje technikům DevOps automatizovat a integrovat zřizování, konfiguraci a operace spravovaných serverů a databází MySQL v Azure.  Rozhraní API umožňuje vytvořit, vyčíslit, spravovat a odstranit servery a databáze MySQL ve službě Azure Database for MySQL.

Azure Resource Manager využít základní REST API k deklarování a programování prostředků Azure potřebných pro nasazení ve velkém měřítku, a to v souladu s infrastrukturou jako koncept kódu. Šablona parameterizes název prostředku Azure, SKU, síť, konfiguraci brány firewall a nastavení, což umožňuje, aby se vytvořila jednorázově a použila se víckrát.  Šablony Azure Resource Manager lze snadno vytvořit pomocí [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal) nebo [Visual Studio Code](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code?tabs=CLI). Umožňují vytváření balíčků, standardizace a nasazení aplikací, které lze integrovat do kanálu CI/CD DevOps.  Pokud například chcete rychle nasadit webovou aplikaci s Azure Database for MySQL back-end, můžete provést kompletní nasazení pomocí této [šablony pro rychlý Start](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/) z Galerie GitHub.

V tomto kurzu se naučíte, jak používat šablonu Azure Resource Manager a další nástroje:

> [!div class="checklist"]
> * Vytvoření serveru Azure Database for MySQL s koncovým bodem služby virtuální sítě pomocí šablony Azure Resource Manager
> * Použití [nástroje pro příkazový řádek mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) k vytvoření databáze
> * Načíst ukázková data
> * Dotazování dat
> * Aktualizace dat

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Vytvoření serveru Azure Database for MySQL s koncovým bodem služby virtuální sítě pomocí šablony Azure Resource Manager

Chcete-li získat odkaz na šablonu JSON pro server Azure Database for MySQL, vyhledejte odkaz na šablonu [servery Microsoft. DBforMySQL](/azure/templates/microsoft.dbformysql/servers) . Níže je uvedená Ukázková šablona JSON, která se dá použít k vytvoření nového serveru se spuštěným Azure Database for MySQL s koncovým bodem služby virtuální sítě.
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
V této žádosti jsou hodnoty, které je třeba přizpůsobit, následující:
+   `name` – zadejte název serveru MySQL (bez názvu domény).
+   `location` – zadejte platnou oblast datového centra Azure pro server MySQL. Například westus2.
+   `properties/version` – zadejte verzi serveru MySQL, která se má nasadit. Například 5,6 nebo 5,7.
+   `properties/administratorLogin` – zadejte přihlašovací jméno správce MySQL pro server. Přihlašovací jméno správce nemůže být azure_superuser, admin, Administrator, root, Guest ani Public.
+   `properties/administratorLoginPassword` – zadejte heslo pro uživatele správce MySQL uvedeného výše.
+   `properties/sslEnforcement` – Pokud chcete povolit nebo zakázat sslEnforcement, zadejte enabled/disabled.
+   `storageProfile/storageMB` – zadejte maximální velikost zřízené úložiště, která je potřeba pro server v megabajtech. Například 5120.
+   `storageProfile/backupRetentionDays` – zadejte požadovanou dobu uchování zálohy ve dnech. Například 7. 
+   `storageProfile/geoRedundantBackup` – zadejte enabled/disabled v závislosti na požadavcích geografického zotavení po havárii.
+   `sku/tier` – zadejte úroveň Basic, GeneralPurpose nebo MemoryOptimized pro nasazení.
+   `sku/capacity` – zadejte kapacitu vCore. Možné hodnoty zahrnují 2, 4, 8, 16, 32 nebo 64.
+   `sku/family` – zadejte Gen5 pro výběr generování hardwaru pro nasazení serveru.
+   `sku/name` – zadejte TierPrefix_family_capacity. Například B_Gen5_1, GP_Gen5_16, MO_Gen5_32. V dokumentaci k [cenovým úrovním](./concepts-pricing-tiers.md) najdete informace o platných hodnotách pro jednotlivé oblasti a na úrovni.
+   `resources/properties/virtualNetworkSubnetId` – zadejte identifikátor Azure podsítě ve virtuální síti, kam se má umístit server Azure MySQL. 
+   `tags(optional)` – zadejte volitelné značky jsou páry klíč-hodnota, které byste použili k kategorizaci prostředků pro fakturaci atd.

Pokud chcete vytvořit šablonu Azure Resource Manager pro automatizaci nasazení Azure Database for MySQL ve vaší organizaci, doporučujeme začít od [šablony ukázka Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) v galerii GitHubu pro rychlý Start Azure. nejprve a sestavte ho nad ním. 

Pokud Azure Resource Manager šablony a chcete si je vyzkoušet, můžete začít pomocí následujících kroků:
+   Naklonujte nebo Stáhněte vzorovou [šablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) z Galerie Azure pro rychlý Start.  
+   Úpravou souboru azuredeploy. Parameters. JSON aktualizujte hodnoty parametrů na základě vaší předvolby a uložte soubor. 
+   Pomocí Azure CLI vytvořte server Azure MySQL pomocí následujících příkazů.

Můžete použít Azure Cloud Shell v prohlížeči nebo nainstalovat rozhraní příkazového řádku Azure CLI do svého počítače, aby se v tomto kurzu spouštěly bloky kódu.

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
> * Vytvoření serveru Azure Database for MySQL s koncovým bodem služby virtuální sítě pomocí šablony Azure Resource Manager
> * Použití [nástroje pro příkazový řádek mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) k vytvoření databáze
> * Načíst ukázková data
> * Dotazování dat
> * Aktualizace dat
> 
> [Postup připojení aplikací k Azure Database for MySQL](./howto-connection-string.md)
