---
title: 'Kurz: Vytvoření šablony Azure Database for MySQL-Azure Resource Manager'
description: V tomto kurzu se dozvíte, jak zřídit a automatizovat Azure Database for MySQL nasazení na serverech pomocí Azure Resource Manager šablony.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/02/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fd5fa4e09dd3f73aa7a8f044ded04d504bd2f3dd
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705468"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>Kurz: zřízení Azure Database for MySQL serveru pomocí šablony Azure Resource Manager

[Azure Database for MySQL REST API](/rest/api/mysql/) umožňuje technikům DevOps automatizovat a integrovat zřizování, konfiguraci a operace spravovaných serverů a databází MySQL v Azure.  Rozhraní API umožňuje vytvořit, vyčíslit, spravovat a odstranit servery a databáze MySQL ve službě Azure Database for MySQL.

Azure Resource Manager využívá základní REST API k deklarování a programovému naprogramování prostředků Azure potřebných pro nasazení ve velkém měřítku, které se zarovnává s infrastrukturou jako koncept kódu. Šablona parameterizes název prostředku Azure, SKU, síť, konfiguraci brány firewall a nastavení, což umožňuje, aby se vytvořila jednorázově a použila se víckrát.  Šablony Azure Resource Manager lze snadno vytvořit pomocí [Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) nebo [Visual Studio Code](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=CLI). Umožňují vytváření balíčků, standardizace a nasazení aplikací, které lze integrovat do kanálu CI/CD DevOps.  Pokud například chcete rychle nasadit webovou aplikaci s Azure Database for MySQL back-end, můžete provést kompletní nasazení pomocí této [šablony pro rychlý Start](https://azure.microsoft.com/resources/templates/101-webapp-managed-mysql/) z Galerie GitHub.

V tomto kurzu se naučíte, jak používat šablonu Azure Resource Manager a další nástroje:

> [!div class="checklist"]
> * Vytvoření serveru Azure Database for MySQL s koncovým bodem služby virtuální sítě pomocí šablony Azure Resource Manager
> * Vytvoření databáze pomocí [Nástroje pro příkazový řádek MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)
> * Načtení ukázkových dat
> * Dotazování dat
> * Aktualizace dat

## <a name="prerequisites"></a>Požadavky

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
+   `name` -Zadejte název serveru MySQL (bez názvu domény).
+   `location` – Zadejte platnou oblast datového centra Azure pro server MySQL. Například westus2.
+   `properties/version` -Zadejte verzi serveru MySQL, která se má nasadit. Například 5,6 nebo 5,7.
+   `properties/administratorLogin` -Zadejte přihlašovací jméno správce MySQL pro server. Přihlašovací jméno správce nemůže být azure_superuser, admin, administrator, root, guest ani public.
+   `properties/administratorLoginPassword` – Zadejte heslo pro uživatele správce MySQL uvedeného výše.
+   `properties/sslEnforcement` – Pokud chcete povolit nebo zakázat sslEnforcement, zadejte enabled/disabled.
+   `storageProfile/storageMB` -Zadejte maximální velikost zřízené úložiště, která je požadována pro server v megabajtech. Například 5120.
+   `storageProfile/backupRetentionDays` -Zadejte požadovanou dobu uchovávání záloh ve dnech. Například 7. 
+   `storageProfile/geoRedundantBackup` -Zadejte enabled/disabled v závislosti na požadavcích geografického zotavení po havárii.
+   `sku/tier` – Zadejte úroveň Basic, GeneralPurpose nebo MemoryOptimized pro nasazení.
+   `sku/capacity` -Zadejte kapacitu vCore. Možné hodnoty zahrnují 2, 4, 8, 16, 32 nebo 64.
+   `sku/family` -Zadejte Gen5 pro výběr generování hardwaru pro nasazení serveru.
+   `sku/name` -Zadat TierPrefix_family_capacity. Například B_Gen5_1 GP_Gen5_16 MO_Gen5_32. V dokumentaci k [cenovým úrovním](./concepts-pricing-tiers.md) najdete informace o platných hodnotách pro jednotlivé oblasti a na úrovni.
+   `resources/properties/virtualNetworkSubnetId` – Zadejte identifikátor Azure podsítě ve virtuální síti, kam se má umístit server Azure MySQL. 
+   `tags(optional)` -Zadat volitelné značky jsou páry klíč-hodnota, které byste použili k kategorizaci prostředků pro fakturaci atd.

Pokud chcete vytvořit šablonu Azure Resource Manager pro automatizaci nasazení Azure Database for MySQL pro vaši organizaci, doporučujeme nejdřív začít od šablony vzorové [Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) v galerii GitHub pro rychlý Start v Azure a sestavovat ji nad ní. 

Pokud Azure Resource Manager šablony a chcete si je vyzkoušet, můžete začít pomocí následujících kroků:
+   Naklonujte nebo Stáhněte vzorovou [šablonu Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) z Galerie Azure pro rychlý Start.  
+   Upravte azuredeploy.parameters.jsna pro aktualizaci hodnot parametrů podle vaší předvolby a uložte soubor. 
+   Pomocí Azure CLI vytvořte server Azure MySQL pomocí následujících příkazů.

Ke spuštění bloků kódu v tomto kurzu můžete použít Azure Cloud Shell v prohlížeči nebo si nainstalujte Azure CLI ve vašem počítači.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l "West US2"
az deployment group create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, která odstraní prostředky ve skupině prostředků.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **skupiny prostředků**.

2. V seznamu Skupina prostředků vyberte název vaší skupiny prostředků.

3. Na stránce **Přehled** vaší skupiny prostředků vyberte **Odstranit skupinu prostředků**.

4. V potvrzovacím dialogovém okně zadejte název vaší skupiny prostředků a pak vyberte **Odstranit**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:
> [!div class="checklist"]
> * Vytvoření serveru Azure Database for MySQL s koncovým bodem služby virtuální sítě pomocí šablony Azure Resource Manager
> * Vytvoření databáze pomocí nástroje pro příkazový řádek mysql
> * Načtení ukázkových dat
> * Dotazování dat
> * Aktualizace dat

> [!div class="nextstepaction"]
> [Postup připojení aplikací k Azure Database for MySQL](./howto-connection-string.md)