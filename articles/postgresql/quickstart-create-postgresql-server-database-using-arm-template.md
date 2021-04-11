---
title: 'Rychlý Start: Vytvoření šablony Azure DB pro PostgreSQL – ARM'
description: V tomto rychlém startu se dozvíte, jak vytvořit Azure Database for PostgreSQL jediný server pomocí šablony Azure Resource Manager.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 02/11/2021
ms.openlocfilehash: db65a4bb5137da23f125d67920a2ff9a1a801141
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606716"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---single-server"></a>Rychlý Start: použití šablony ARM k vytvoření serveru Azure Database for PostgreSQL-Single

Azure Database for PostgreSQL je spravovaná služba, pomocí které spouštíte, spravujete a škálujete vysoce dostupné databáze PostgreSQL v cloudu. V tomto rychlém startu pomocí šablony Azure Resource Manager (šablona ARM) vytvoříte Azure Database for PostgreSQL jeden server v Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-postgresql-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* Pokud chcete spustit kód místně, [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* Pokud chcete spustit kód místně, [Azure CLI](/cli/azure/).

---

## <a name="review-the-template"></a>Kontrola šablony

Vytvoříte Azure Database for PostgreSQL Server s nakonfigurovanou sadou výpočetních prostředků a prostředků úložiště. Další informace najdete v tématu [cenové úrovně na Azure Database for PostgreSQL – jeden server](concepts-pricing-tiers.md). Server vytvoříte v rámci [skupiny prostředků Azure](../azure-resource-manager/management/overview.md).

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-managed-postgresql-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-postgresql-with-vnet/azuredeploy.json":::

Šablona definuje pět prostředků Azure:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/virtualNetworks/podsítí**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft. DBforPostgreSQL/servery**](/azure/templates/microsoft.dbforpostgresql/servers)
* [**Microsoft. DBforPostgreSQL/servery/virtualNetworkRules**](/azure/templates/microsoft.dbforpostgresql/servers/virtualnetworkrules)
* [**Microsoft. DBforPostgreSQL/servery/firewallRules**](/azure/templates/microsoft.dbforpostgresql/servers/firewallrules)

Další ukázky šablon Azure Database for PostgreSQL najdete v [šablonách rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbforpostgresql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Vyberte následující odkaz pro nasazení šablony Azure Database for PostgreSQL serveru do Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-postgresql-with-vnet%2fazuredeploy.json)

Na stránce **nasadit Azure Database for PostgreSQL se sítí VNET** :

1. V poli **Skupina prostředků** vyberte **vytvořit novou**, zadejte název nové skupiny prostředků a vyberte **OK**.

2. Pokud jste vytvořili novou skupinu prostředků, vyberte **umístění** pro skupinu prostředků a nový server.

3. Zadejte **název serveru**, **přihlašovací jméno správce** a **přihlašovací heslo správce**.

    :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-arm-template/deploy-azure-database-for-postgresql-with-vnet.png" alt-text="Nasazení Azure Database for PostgreSQL s oknem VNet, šablonou Azure pro rychlý Start, Azure Portal":::

4. Pokud chcete, změňte další výchozí nastavení:

    * **Předplatné**: předplatné Azure, které chcete použít pro server.
    * **Kapacita SKU**: kapacita Vcore, která může být *2* (výchozí), *4*, *8*, *16*, *32* nebo *64*.
    * **Název SKU**: Předpona úrovně SKU, rodina SKU a kapacita skladové položky, které jsou spojeny podtržítky, například *B_Gen5_1*, *GP_Gen5_2* (výchozí) nebo *MO_Gen5_32*.
    * **Velikost SKU MB**: velikost úložiště pro Azure Database for PostgreSQL Server (standardně *51200*) v megabajtech.
    * **Úroveň SKU**: vrstva nasazení, jako je například *Basic*, *GeneralPurpose* (výchozí), nebo *MemoryOptimized*.
    * **Rodina SKU**: *COMPUTE GEN4 –* nebo *Gen5* (výchozí), která indikuje generování hardwaru pro nasazení serveru.
    * **Verze PostgreSQL**: verze serveru PostgreSQL, která se má nasadit, například *9,5*, *9,6*, *10* nebo *11* (výchozí).
    * **Dny uchovávání záloh**: požadované období pro uchování geograficky redundantního zálohování ve dnech (výchozí *7*).
    * **Geograficky redundantní zálohování**: *povoleno* nebo *zakázáno* (výchozí nastavení), v závislosti na geograficky se zotavení po havárii (GEO-Dr).
    * **Virtual Network název**: název virtuální sítě (výchozí *azure_postgresql_vnet*).
    * **Název podsítě**: název podsítě (výchozí *azure_postgresql_subnet*).
    * **Virtual Network název pravidla**: název pravidla virtuální sítě, které povoluje podsíť (výchozí *AllowSubnet*).
    * **Předpona adresy virtuální** sítě: Předpona adresy virtuální sítě (výchozí *10.0.0.0/16*).
    * **Předpona podsítě**: Předpona adresy pro podsíť (výchozí *10.0.0.0/16*).

5. Přečtěte si podmínky a ujednání a potom vyberte Souhlasím **s výše uvedenými podmínkami a ujednáními**.

6. Vyberte **Koupit**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Pomocí následujícího interaktivního kódu vytvořte nový Azure Database for PostgreSQL Server pomocí šablony. Kód vás vyzve k zadání nového názvu serveru, názvu a umístění nové skupiny prostředků a jména a hesla účtu správce.

Chcete-li spustit kód v Azure Cloud Shell, vyberte **vyzkoušet** v horním rohu libovolného bloku kódu.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-postgresql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

Pomocí následujícího interaktivního kódu vytvořte nový Azure Database for PostgreSQL Server pomocí šablony. Kód vás vyzve k zadání nového názvu serveru, názvu a umístění nové skupiny prostředků a jména a hesla účtu správce.

Chcete-li spustit kód v Azure Cloud Shell, vyberte **vyzkoušet** v horním rohu libovolného bloku kódu.

```azurecli-interactive
read -p "Enter a name for the new Azure Database for PostgreSQL server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for PostgreSQL server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-postgresql-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pomocí těchto kroků můžete zobrazit přehled nového serveru Azure Database for PostgreSQL:

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **Azure Database for PostgreSQL servery**.

2. V seznamu databáze vyberte nový server. Zobrazí se stránka s **přehledem** nového serveru Azure Database for PostgreSQL.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Chcete-li zobrazit podrobnosti o serveru Azure Database for PostgreSQL, spusťte následující interaktivní kód. Bude nutné zadat název nového serveru.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for PostgreSQL server"
Get-AzResource -ResourceType "Microsoft.DbForPostgreSQL/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

Chcete-li zobrazit podrobnosti o serveru Azure Database for PostgreSQL, spusťte následující interaktivní kód. Budete muset zadat název a skupinu prostředků nového serveru.

```azurecli-interactive
read -p "Enter your Azure Database for PostgreSQL server name: " serverName &&
read -p "Enter the resource group where the Azure Database for PostgreSQL server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForPostgreSQL/servers" &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="exporting-arm-template-from-the-portal"></a>Export šablony ARM z portálu
[Šablonu ARM můžete exportovat](../azure-resource-manager/templates/export-template-portal.md) z Azure Portal. Existují dva způsoby, jak exportovat šablonu:

- [Exportujte ze skupiny prostředků nebo prostředku](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource). Tato možnost vygeneruje novou šablonu z existujících prostředků. Vyexportovaná šablona je "snímek" aktuálního stavu skupiny prostředků. V rámci této skupiny prostředků můžete exportovat celou skupinu prostředků nebo konkrétní prostředky.
- [Exportujte před nasazením nebo z historie](../azure-resource-manager/templates/export-template-portal.md#export-template-before-deployment). Tato možnost načte přesnou kopii šablony, která se používá k nasazení.

Při exportování šablony ```"properties":{ }```  si všimněte, že v části prostředku serveru PostgreSQL si všimnete, že ```administratorLogin``` a nebudou ```administratorLoginPassword``` zahrnuty z bezpečnostních důvodů. Tyto parametry **musíte** před nasazením šablony přidat do šablony, jinak se šablona nezdařila.

```
"resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "name": "[parameters('servers_name')]",
      "location": "southcentralus",
      "sku": {
                "name": "B_Gen5_1",
                "tier": "Basic",
                "family": "Gen5",
                "capacity": 1
            },
      "properties": {
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
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
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
read -p "Enter the Resource Group name: " resourceGroupName &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="next-steps"></a>Další kroky

Podrobný kurz, který vás provede procesem vytvoření šablony, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [ Kurz: vytvoření a nasazení první šablony ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
