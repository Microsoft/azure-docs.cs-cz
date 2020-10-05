---
title: 'Rychlý Start: Vytvoření šablony Azure DB pro MariaDB – ARM'
description: V tomto článku rychlý Start se dozvíte, jak vytvořit Azure Database for MariaDB Server pomocí Azure Resource Manager šablony.
author: mgblythe
ms.service: mariadb
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mblythe
ms.date: 05/14/2020
ms.openlocfilehash: 108233892a89e6f6288e05476e37460a58b52b0e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88660542"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mariadb-server"></a>Rychlý Start: použití šablony ARM k vytvoření serveru Azure Database for MariaDB

Azure Database for MariaDB je spravovaná služba, pomocí které můžete spouštět, spravovat a škálovat vysoce dostupné databáze MariaDB v cloudu. V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k vytvoření Azure Database for MariaDBho serveru v Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

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

Server Azure Database for MariaDB vytvoříte s definovanou sadou výpočetních prostředků a prostředků úložiště. Další informace najdete v tématu [Azure Database for MariaDB cenové úrovně](concepts-pricing-tiers.md). Server vytvoříte v rámci [skupiny prostředků Azure](../azure-resource-manager/management/overview.md).

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-managed-mariadb-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-mariadb-with-vnet/azuredeploy.json":::

Šablona definuje pět prostředků Azure:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/virtualNetworks/podsítí**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft. DBforMariaDB/servery**](/azure/templates/microsoft.dbformariadb/servers)
* [**Microsoft. DBforMariaDB/servery/virtualNetworkRules**](/azure/templates/microsoft.dbformariadb/servers/virtualnetworkrules)
* [**Microsoft. DBforMariaDB/servery/firewallRules**](/azure/templates/microsoft.dbformariadb/servers/firewallrules)

Další ukázky šablon Azure Database for MariaDB najdete v [šablonách rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformariadb&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Vyberte následující odkaz pro nasazení šablony Azure Database for MariaDB serveru do Azure Portal:

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mariadb-with-vnet%2fazuredeploy.json)

Na stránce **nasadit Azure Database for MariaDB se sítí VNET** :

1. V poli **Skupina prostředků**vyberte **vytvořit novou**, zadejte název nové skupiny prostředků a vyberte **OK**.

2. Pokud jste vytvořili novou skupinu prostředků, vyberte **umístění** pro skupinu prostředků a nový server.

3. Zadejte **název serveru**, **přihlašovací jméno správce**a **přihlašovací heslo správce**.

    ![Nasazení Azure Database for MariaDB s oknem VNet, šablonou Azure pro rychlý Start, Azure Portal](./media/quickstart-create-mariadb-server-database-arm-template/deploy-azure-database-mariadb-vnet.png)

4. Pokud chcete, změňte další výchozí nastavení:

    * **Předplatné**: předplatné Azure, které chcete použít pro server.
    * **Kapacita SKU**: kapacita Vcore, která může být *2* (výchozí), *4*, *8*, *16*, *32*nebo *64*.
    * **Název SKU**: Předpona úrovně SKU, rodina SKU a kapacita skladové položky, které jsou spojeny podtržítky, například *B_Gen5_1*, *GP_Gen5_2* (výchozí) nebo *MO_Gen5_32*.
    * **Velikost SKU MB**: velikost úložiště pro Azure Database for MariaDB Server (standardně *51200*) v megabajtech.
    * **Úroveň SKU**: vrstva nasazení, jako je například *Basic*, *GeneralPurpose* (výchozí), nebo *MemoryOptimized*.
    * **Rodina SKU**: *COMPUTE GEN4 –* nebo *Gen5* (výchozí), která indikuje generování hardwaru pro nasazení serveru.
    * **Verze MariaDB**: verze serveru MariaDB, která se má nasadit, například *10,2*nebo *10,3* (výchozí).
    * **Dny uchovávání záloh**: požadované období pro uchování geograficky redundantního zálohování ve dnech (výchozí *7*).
    * **Geograficky redundantní zálohování**: *povoleno* nebo *zakázáno* (výchozí nastavení), v závislosti na geograficky se zotavení po havárii (GEO-Dr).
    * **Virtual Network název**: název virtuální sítě (výchozí *azure_mariadb_vnet*).
    * **Název podsítě**: název podsítě (výchozí *azure_mariadb_subnet*).
    * **Virtual Network název pravidla**: název pravidla virtuální sítě, které povoluje podsíť (výchozí *AllowSubnet*).
    * **Předpona adresy virtuální**sítě: Předpona adresy virtuální sítě (výchozí *10.0.0.0/16*).
    * **Předpona podsítě**: Předpona adresy pro podsíť (výchozí *10.0.0.0/16*).

5. Přečtěte si podmínky a ujednání a potom vyberte Souhlasím **s výše uvedenými podmínkami a ujednáními**.

6. Vyberte **Koupit**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Pomocí následujícího interaktivního kódu vytvořte nový Azure Database for MariaDB Server pomocí šablony. Kód vás vyzve k zadání nového názvu serveru, názvu a umístění nové skupiny prostředků a jména a hesla účtu správce.

Chcete-li spustit kód v Azure Cloud Shell, vyberte **vyzkoušet** v horním rohu libovolného bloku kódu.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MariaDB server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MariaDB server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mariadb-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

Pomocí následujícího interaktivního kódu vytvořte nový Azure Database for MariaDB Server pomocí šablony. Kód vás vyzve k zadání nového názvu serveru, názvu a umístění nové skupiny prostředků a jména a hesla účtu správce.

Chcete-li spustit kód v Azure Cloud Shell, vyberte **vyzkoušet** v horním rohu libovolného bloku kódu.

```azurecli-interactive
read -p "Enter a name for the new Azure Database for MariaDB server:" serverName &&
read -p "Enter a name for the new resource group where the server will exist:" resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group:" location &&
read -p "Enter the Azure Database for MariaDB server's administrator account name:" adminUser &&
read -p "Enter the administrator password:" adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mariadb-with-vnet/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pomocí těchto kroků můžete zobrazit přehled nového serveru Azure Database for MariaDB:

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **Azure Database for MariaDB servery**.

2. V seznamu databáze vyberte nový server. Zobrazí se stránka s **přehledem** nového serveru Azure Database for MariaDB.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Chcete-li zobrazit podrobnosti o serveru Azure Database for MariaDB, spusťte následující interaktivní kód. Bude nutné zadat název nového serveru.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MariaDB server"
Get-AzResource -ResourceType "Microsoft.DbForMariaDB/servers" -Name $serverName | ft
Read-Host -Prompt "Press [ENTER] to continue: "
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

Chcete-li zobrazit podrobnosti o serveru Azure Database for MariaDB, spusťte následující interaktivní kód. Budete muset zadat název a skupinu prostředků nového serveru.

```azurecli-interactive
read -p "Enter your Azure Database for MariaDB server name: " serverName &&
read -p "Enter the resource group where the Azure Database for MariaDB server exists: " resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMariaDB/servers" &&
read -p "Press [ENTER] to continue: "
```

---

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

Podrobný kurz, který vás provede procesem vytvoření šablony ARM, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [ Kurz: vytvoření a nasazení první šablony ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
